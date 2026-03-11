# Architecture

## Communication Flow

The driver sits between EPICS records and the Galil hardware, translating PV operations into Galil 2-letter ASCII commands and parsing binary data records back into PV values.

### Write Path (EPICS to Hardware)

```
EPICS Records (PVs)
    |
    v
asyn interface (writeInt32 / writeFloat64 / writeOctet)
    |
    v
GalilController   -- formats 2-letter Galil command (e.g. "SP A=5000")
    |
    v
TCP connection     -- sends command, receives ASCII response
    |
    v
DMC hardware       -- executes command
```

### Read Path (Hardware to EPICS)

```
DMC hardware
    |
    v
Data Record (binary status blob, pushed via UDP or polled via TCP)
    |
    v
GalilPoller thread -- parses binary fields from data record
    |
    v
asyn ParamList     -- updates parameter values
    |
    v
Callbacks          -- I/O Intr scan triggers record processing
    |
    v
EPICS Records (PVs)
```

---

## Key Classes

### GalilController

Main driver class, extends `asynMotorController`. One instance per physical controller.

- Manages TCP connection to the hardware and all command traffic.
- Translates PV writes into Galil 2-letter ASCII commands using the internal `cmd_` buffer for command formatting.
- Owns the asyn parameter list that maps PV indices to values.
- Handles code upload/download, profile moves, and analog/digital I/O.
- Creates and manages `GalilAxis` and `GalilCSAxis` objects.
- Auto-generates DMC code that runs on the controller for home routines, limit handling, and digital I/O monitoring.

### GalilAxis

Per-axis motor control for real axes A--H. Extends `asynMotorAxis`.

- Implements `move()`, `home()`, `stop()`, `setPosition()`, and `poll()`.
- Manages enable/disable, PID tuning, velocity, and acceleration.
- Handles limit switch configuration and encoder setup (including SSI and BiSS absolute encoders).
- Parses per-axis fields from the data record during polling.

### GalilCSAxis

Coordinate system axis control for CS axes I--P. Extends `asynMotorAxis`.

- Maps onto Galil coordinate systems S and T (up to 4 axes per coordinate system).
- Handles forward and reverse kinematic transforms.
- Coordinates multi-axis moves so that axes start and stop together.
- Uses the Galil `CA`, `CD`, `CR` (vector mode) commands for coordinated motion.

### GalilPoller

Dedicated polling thread that keeps PV values synchronized with hardware state.

- In async UDP mode, the controller pushes data records at the configured rate using the Galil `DR` command. The poller receives and parses these binary packets.
- In synchronous TCP mode, the poller requests data records on a timer.
- Parses binary data record fields into individual axis positions, velocities, status bits, analog values, and digital I/O states.
- Updates the asyn parameter list, which triggers `I/O Intr` callbacks to process EPICS records.

### GalilConnector

Connection management thread.

- Monitors the TCP connection and handles reconnection on loss of communication.
- Manages initial connection setup and controller identification.

---

## Communication Modes

| Mode | Transport | Mechanism | Use Case |
|---|---|---|---|
| Async UDP | Ethernet | Controller pushes data records at a rate configured by the `DR` command | Preferred mode for ethernet-connected controllers. Lowest latency. |
| Synchronous TCP | Ethernet | Driver polls on a timer, requesting data records over the command TCP connection | Fallback when UDP is unavailable, or when a negative `updatePeriod` is specified in `GalilCreateController`. |
| Serial (RS232) | RS-232 | Synchronous polling only | Used for serial-connected controllers. No async data record support. |

---

## Data Flow Details

### Write Operations

A PV write follows this path:

1. An EPICS record processes (e.g., a user writes to a motor record `.VAL` field or a direct `caput` to a driver PV).
2. The asyn layer calls the appropriate write method on `GalilController`: `writeInt32`, `writeFloat64`, or `writeOctet`, depending on the parameter type.
3. `GalilController` identifies the parameter by its asyn index and formats the corresponding Galil 2-letter command in the `cmd_` buffer (e.g., `"SP A=5000"` for setting speed on axis A).
4. The command is sent over the TCP connection to the controller.
5. The ASCII response is parsed for errors or return values.

### Read Operations

Hardware state reaches EPICS through the data record:

1. `GalilPoller` receives the data record -- either pushed asynchronously via UDP or fetched synchronously via TCP.
2. The binary data record is parsed field by field. Each axis contributes a fixed-size block containing position, velocity, torque, status flags, limit states, and other values.
3. Parsed values are written into the asyn parameter list via `setIntegerParam`, `setDoubleParam`, etc.
4. `callParamCallbacks()` fires, and any records configured with `SCAN=I/O Intr` on the corresponding asyn reasons are processed with the new values.

---

## Key Constants

| Constant | Value | Description |
|---|---|---|
| `MAX_GALIL_AXES` | 8 | Maximum real axes per controller (A--H) |
| `MAX_GALIL_CSAXES` | 8 | Maximum coordinate system axes (I--P) |
| `COORDINATE_SYSTEMS` | 2 | Number of coordinate systems (S, T) |
| `ANALOG_PORTS` | 8 | Analog input/output channels |
| `BINARYIN_BYTES` | 3 | Bytes of binary (digital) input data |
| `BINARYOUT_WORDS` | 3 | Words of binary (digital) output data |

---

## Auto-Generated DMC Code

The driver automatically generates and downloads DMC code to the controller at IOC startup. This on-controller code handles:

- **Home routines** -- sequences the home switch search, index pulse capture, and position correction for each axis.
- **Limit handling** -- automatic stop and status reporting when hardware or software limits are hit.
- **Digital I/O monitoring** -- on-controller threads that watch digital inputs and execute configured actions without round-trip latency to the IOC.

The generated code is assembled from templates based on the axis configuration specified in the IOC startup script.

---

## PV Naming Convention

PVs follow this pattern:

```
$(P)$(M)$(R)
```

| Macro | Purpose | Example |
|---|---|---|
| `$(P)` | IOC / controller prefix | `DMC01:` |
| `$(M)` | Motor or axis identifier | `A`, `B`, ... `H` for real axes; `I`, ... `P` for CS axes |
| `$(R)` | Record name component | Specific to each PV (e.g., `Jog`, `HomProc`, `ErrCode`) |

For controller-level PVs that are not axis-specific, only `$(P)` and `$(R)` are used. For I/O PVs, the axis macro may be replaced by a port or channel number.
