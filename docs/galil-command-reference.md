# Appendix: Galil Command Reference

Reference of Galil 2-letter commands used by the IOC driver. Full documentation is available in the *Galil Command Reference* manual from Galil Motion Control.

Units throughout are **counts** (position), **counts/sec** (velocity), **counts/sec^2** (acceleration), unless otherwise noted. Axis letters A--H are appended to commands (e.g., `SPA=10000` sets axis A speed).

---

## Motion Commands

### BG

**Begin** -- Start motion on one or more axes or a coordinate system.

Syntax: `BG axes` or `BG S` / `BG T`

`BGX` begins motion on axis X. `BGAB` begins axes A and B simultaneously. `BGS` begins coordinated motion on coordinate system S. Motion parameters (PA/PR, SP, AC, DC) must be set before issuing BG.

The IOC issues BG after setting up a move through the motor record. In deferred-move mode, BG is held until the `DEFER_CMD` PV is set back to "Go", allowing multiple axes to start simultaneously.

### ST

**Stop** -- Decelerate to a stop.

Syntax: `ST axes` or `ST S` / `ST T`

`STX` stops axis X. `STS` stops coordinate system S. The axis decelerates at the rate set by DC (or SD if configured for limit-switch stops).

The IOC issues ST when the motor record `.STOP` field is processed. In "Sync start/stop" deferred mode, ST is also deferred so multiple axes stop together.

### PA

**Position Absolute** -- Command an absolute move to a target position.

Syntax: `PAX=n`

`PAN=25000` commands axis N to move to absolute position 25000 counts. The motor record writes PA for absolute moves when UEIP=0 or for dial-coordinate moves.

### PR

**Position Relative** -- Command a relative move from the current position.

Syntax: `PRX=n`

`PRA=1000` moves axis A 1000 counts forward from its current position. `PRA=-500` moves 500 counts in reverse. The motor record uses PR for relative moves and backlash correction (`.BDST`).

### JG

**Jog** -- Command continuous motion at a specified velocity.

Syntax: `JGX=n`

`JGA=5000` jogs axis A at 5000 counts/sec. Negative values reverse direction. Unlike PA/PR moves, JG motion continues indefinitely until stopped with ST.

The IOC uses JG for velocity-mode (`.JOGF` / `.JOGR`) moves and for the "jog after home" feature.

### SP

**Speed** -- Set the slew speed for position moves (PA/PR).

Syntax: `SPX=n`

`SPA=25000` sets axis A slew speed to 25000 counts/sec. This does not affect JG moves (JG specifies its own velocity). Range: 0 to 12,000,000 (controller-dependent).

The motor record `.VELO` field maps to SP.

### AC

**Acceleration** -- Set acceleration rate for independent moves.

Syntax: `ACX=n`

`ACA=256000` sets axis A acceleration to 256000 counts/sec^2. Applies to PA, PR, and JG moves. Range: 1024 to 1,073,740,800.

The driver calculates counts/sec^2 from the motor record `.VELO` and `.ACCL` fields.

### DC

**Deceleration** -- Set deceleration rate for independent moves.

Syntax: `DCX=n`

Same units and range as AC. Also used as the default stop rate for ST commands and limit-switch deceleration if SD is not set.

### IT

**Independent Time Constant** -- S-curve smoothing filter for independent moves.

Syntax: `ITX=n`

Range 0.0--1.0. `ITA=1.0` means no smoothing (trapezoidal profile). Lower values smooth the velocity transitions. `ITA=0.5` gives a moderate S-curve. Default 1.0.

### HM

**Home** -- Begin a homing sequence using the home switch and/or index pulse.

Syntax: `HMX`

Behavior depends on the CN switch polarity settings and the home type configured via PVs. The IOC does not issue HM directly -- it implements homing through auto-generated DMC code on the controller that sequences HM, FI, and FE in various combinations depending on the selected homing strategy.

### FI

**Find Index** -- Jog until the encoder index pulse is detected, then stop and capture position.

Syntax: `FIX`

After detecting the index pulse, the controller captures the exact position and decelerates to a stop. Used as part of homing routines for precise reference to the encoder index. The IOC's generated home code may use FI after HM or FE to refine the home position.

### FE

**Find Edge** -- Jog until a home switch transition is detected, then stop.

Syntax: `FEX`

The controller monitors the home input and stops when a transition occurs. The direction of the edge (rising/falling) depends on CN configuration. Used as part of homing routines to precisely locate the switch edge.

---

## Motor Configuration

### MT

**Motor Type** -- Set the motor type for an axis.

Syntax: `MTX=n`

| Value | Motor Type |
|-------|------------|
| 1.0 | Servo |
| -1.0 | Reversed servo |
| 2.0 | High active stepper |
| -2.0 | Reversed high active stepper |
| 2.5 | High active stepper with encoder |
| -2.5 | Reversed high active stepper with encoder |
| 3.0 | Low active stepper |
| -3.0 | Reversed low active stepper |
| 3.5 | Low active stepper with encoder |
| -3.5 | Reversed low active stepper with encoder |

Negative values reverse the direction convention. Stepper "with encoder" types (2.5, 3.5) enable step-count correction against the encoder.

The IOC sets MT from the `_MTRTYPE_CMD` PV. It is saved/restored by autosave at IOC start.

### CE

**Configure Encoder** -- Set the encoder type per axis.

Syntax: `CEX=n`

| Value | Encoder Type |
|-------|--------------|
| 0 | Normal quadrature |
| 2 | Pulse and direction |
| 4 | Reversed quadrature |
| 6 | Reversed pulse and direction |
| 8 | Reversed quadrature, aux reversed |
| 10 | Reversed pulse/dir, aux reversed |
| 12 | Normal quadrature, aux reversed |
| 14 | Pulse and direction, aux reversed |

The IOC sets CE from the `_MENCTYPE_CMD` and `_AENCTYPE_CMD` PVs.

### BR

**Brush Axis** -- Set brushless motor commutation mode.

Syntax: `BRX=n`

`0` = brush motor (default, no commutation). `1` = sinusoidal commutation using hall sensors. `2` = external commutation. The IOC exposes this through the `_BRTYPE_CMD` PV.

### AG

**Amplifier Gain** -- Set the DAC output voltage range for the amplifier.

Syntax: `AGX=n`

| Value | DAC Range |
|-------|-----------|
| 0 | +/-5V (default) |
| 1 | 0--5V |
| 2 | 0--10V |
| 3 | +/-10V |
| 5 | +/-12V |

Determines how the controller's DAC output scales to amplifier input. The IOC exposes this through the `_AMPGAIN_CMD` PV.

### AU

**Amplifier Current Loop** -- Configure the current loop for Galil integrated amplifiers.

Syntax: `AUX=a,b,c`

Configures up to three current-loop parameters for integrated amplifier axes. Parameter meanings depend on the amplifier model. The IOC exposes this through the `_AMPCLGAIN_CMD` PV.

### KS

**Step Motor Smoothing** -- Set the smoothing factor for stepper motor output.

Syntax: `KSX=n`

Range: 0.25--64. Default: 2. Lower values produce smoother motion (less vibration) but reduce maximum achievable speed. Higher values allow faster speeds but increase vibration.

### YA

**Step Drive Resolution** -- Set microstep resolution for stepper axes.

Syntax: `YAX=n`

Sets the number of microsteps per full step. Valid values are powers of 2 (e.g., 2, 4, 8, 16, 32, 64, 128, 256). Maximum depends on controller model. Higher resolution gives smoother motion.

### LC

**Low Current Stepper Mode** -- Enable reduced holding current for stepper motors.

Syntax: `LCX=n`

`LCA=0` disables low-current mode (full current at all times). `LCA=1` enables reduced current when the stepper is holding position. Reduces heat dissipation but lowers holding torque.

---

## PID / Servo Tuning

### KP

**Proportional Constant** -- Set the proportional gain of the PID loop.

Syntax: `KPX=n`

Range: 0--1023.875 (14-bit resolution). `KPA=6` sets axis A proportional gain to 6. Active only for servo motor types. The torque output is proportional to the position error multiplied by KP.

### KI

**Integrator** -- Set the integral gain of the PID loop.

Syntax: `KIX=n`

Range: 0--255.875. Accumulates position error over time to eliminate steady-state error. Should be used with IL to prevent integral windup. Excessive KI causes oscillation.

### KD

**Derivative Constant** -- Set the derivative gain of the PID loop.

Syntax: `KDX=n`

Range: 0--4095.875 (16-bit resolution). Provides damping based on the rate of change of position error. Helps reduce overshoot and oscillation.

### ER

**Error Limit** -- Set the maximum allowable position error before faulting.

Syntax: `ERX=n`

Units: counts. If `|position_error| > ER`, the controller declares an error condition. `ERA=5000` sets a 5000-count error limit on axis A. Set to 0 to disable error checking.

The IOC exposes this through the `_ERRLIMIT_SP` PV. When exceeded, the axis stops and the OE setting determines whether the motor is turned off.

### OE

**Off-on-Error** -- Configure behavior when the position error limit (ER) is exceeded.

Syntax: `OEX=n`

`0` = motor remains energized on error (default). `1` = motor is turned off on error. The IOC exposes this through the `_OFFONERR_CMD` PV.

### TL

**Torque Limit** -- Set the continuous torque (DAC output) limit.

Syntax: `TLX=n`

Range: 0--9.998V. `TLA=5.0` limits axis A DAC output to 5V, which limits the current command to the amplifier. Protects the motor, amplifier, and load from excessive force. The motor record `.CNEN` field interacts with this.

### TK

**Peak Torque Limit** -- Set the peak torque limit.

Syntax: `TKX=n`

Same units as TL. Allows briefly exceeding the TL value during acceleration/deceleration transients. Set to 0 to disable the peak limit. Must be >= TL when enabled.

### IL

**Integrator Limit** -- Clamp the maximum contribution of the integrator.

Syntax: `ILX=n`

Range: 0--9.998V. Prevents integral windup by limiting the accumulated integrator output. Should be set to a value less than TL. Default: 9.998.

---

## Position / Encoder

### TP

**Tell Position** -- Read the main encoder position.

Syntax: `TPX` -- returns position in counts.

The IOC reads encoder position from the **data record**, not by issuing TP directly. TP returns the actual encoder count, which may differ from the commanded position (RP) by the position error.

### TD

**Tell Dual Encoder** -- Read the auxiliary (dual) encoder position.

Syntax: `TDX` -- returns position in counts.

Used when a second encoder is mounted (e.g., a direct-reading encoder on the load side, with the primary encoder on the motor). Read from the data record.

### DP

**Define Position** -- Set the main encoder register to a specified value.

Syntax: `DPX=n`

`DPA=0` zeros the encoder register on axis A. Used during homing to define the home position and by the motor record `.SET` field for position recalibration.

### DE

**Dual Encoder Position** -- Set the auxiliary encoder register.

Syntax: `DEX=n`

`DEA=0` zeros the auxiliary encoder on axis A. Used alongside DP when resetting both encoder registers during homing or position correction.

### RP

**Reference Position** -- Read the commanded (reference) position.

Syntax: `RPX` -- returns position in counts.

This is where the controller thinks the axis should be (the theoretical trajectory position), as opposed to TP which is where the encoder says it actually is. The difference `TP - RP` is the position error. Read from the data record.

---

## Limit and Home Switches

### CN

**Configure** -- Set switch polarity for limits, home, and latch inputs.

Syntax: `CN m,n,p,q,r`

| Argument | Description |
|----------|-------------|
| m | Limit switch polarity: -1 = active low, 1 = active high |
| n | Home switch polarity: -1 = active low, 1 = active high |
| p | Latch polarity |
| q | Reserved |
| r | Reserved |

The IOC sets CN from the `LIMITTYPE_CMD`, `HOMETYPE_CMD`, and `HOMEEDGE_CMD` controller PVs. Changing CN affects all axes on the controller. The limit type readback is queried via `MG _CN0`.

### FL

**Forward Software Limit** -- Set the forward software position limit.

Syntax: `FLX=n`

`FLA=100000` prevents axis A from commanding past position 100000. The controller will decelerate to a stop if a move would exceed this limit. Set to 2147483647 to effectively disable.

Mapped from the motor record `.HLM` (high limit) via the driver's dial-to-raw conversion.

### BL

**Reverse Software Limit** -- Set the reverse software position limit.

Syntax: `BLX=n`

`BLA=-100000` prevents axis A from commanding past -100000. Set to -2147483648 to disable.

Mapped from the motor record `.LLM` (low limit) via the driver's dial-to-raw conversion.

### LD

**Limit Disable** -- Disable hardware limit switches per axis.

Syntax: `LDX=n`

Bit mask: bit 0 = forward limit, bit 1 = reverse limit. `LDA=0` enables both limits (default). `LDA=1` disables forward limit. `LDA=2` disables reverse limit. `LDA=3` disables both.

The IOC exposes this through the `_LIMITDISABLE_CMD` PV.

### SD

**Limit Switch Deceleration** -- Set the deceleration rate when a hardware limit is hit.

Syntax: `SDX=n`

Units: counts/sec^2. If not set, the controller uses the DC value for limit-switch deceleration. Setting SD separately allows faster emergency deceleration on limit contact while using a gentler DC for normal stops.

---

## Motor Enable/Disable

### SH

**Servo Here** -- Enable the motor amplifier and close the servo loop.

Syntax: `SHX` or `SH` (all axes)

`SHA` enables axis A. For servo motors, this closes the PID loop and energizes the amplifier. For stepper motors, this energizes the step/direction outputs. The controller immediately applies the PID algorithm and holds position.

The IOC sends SH when the motor is enabled via the `_ON_CMD` PV or when auto-on activates before a move.

### MO

**Motor Off** -- Disable the motor amplifier and open the servo loop.

Syntax: `MOX` or `MO` (all axes)

`MOA` disables axis A. The DAC output goes to zero and the motor is free to move. The IOC sends MO on motor disable via `_ON_CMD` or when auto-off triggers after the configured idle timeout.

---

## I/O Commands

### SB

**Set Bit** -- Set a digital output bit high.

Syntax: `SB n`

`SB 1` sets output bit 1 high. Bit numbers are 1-based. On DMC controllers, bits map to 16-bit output words. On RIO units, bits map to output bytes.

The IOC uses SB for setting digital output bits and for controlling the brake output when auto-brake is configured.

### CB

**Clear Bit** -- Clear a digital output bit low.

Syntax: `CB n`

`CB 1` clears output bit 1. Complement of SB.

### OB

**Output Bit** -- Set a digital output bit to a specified value.

Syntax: `OB n,v`

`OB 1,1` sets bit 1 high. `OB 1,0` sets bit 1 low. Combines the function of SB and CB into a single command. The IOC uses OB for writing digital output PVs.

### AO

**Analog Output** -- Set an analog output voltage.

Syntax: `AO n,v`

`AO 0,3.5` sets analog output channel 0 to 3.5V. Range depends on hardware configuration (typically +/-10V). Channel numbers are 0-based on the command but mapped 1-based on hardware.

The IOC writes AO from the analog output `_SP` PV.

### @AN

**Analog Input Query** -- Read an analog input voltage.

Syntax: `MG @AN[n]`

`MG @AN[0]` reads analog input channel 0. Returns voltage. The IOC reads analog inputs from the **data record** at the polling rate rather than issuing @AN directly, except where the data record does not include analog fields.

### @IN

**Read Digital Input** -- Read a digital input bit.

Syntax: `MG @IN[n]`

`MG @IN[1]` reads input bit 1. Returns 0 or 1. The IOC reads digital inputs from the **data record** at the polling rate.

### @OUT

**Read Digital Output** -- Read back a digital output bit.

Syntax: `MG @OUT[n]`

`MG @OUT[1]` reads output bit 1 state. Returns 0 or 1.

### OC

**Output Compare** -- Configure position-triggered output pulses.

Syntax: `OC axis,start,increment` or `OC axis=start,increment`

`OCA=1000,100` configures axis A to generate a pulse every 100 counts starting at position 1000. Two output compare channels are available: OC1 covers axes A--D, OC2 covers axes E--H.

The IOC configures OC from the `OC1START_CMD`, `OC1INCR_CMD`, `OC1AXIS_CMD` (and OC2 equivalents) controller PVs. Setting the axis to "OFF" disables output compare on that channel.

---

## Absolute Encoders

### SI

**Configure SSI** -- Configure SSI (Synchronous Serial Interface) absolute encoder.

Syntax: `SIX=p,q,r,s`

| Argument | Description |
|----------|-------------|
| p | Total bits in the SSI frame |
| q | Single-turn bits (position resolution within one revolution) |
| r | Data format: 0 = binary, 1 = Gray code |
| s | Direction: 0 = normal, 1 = reversed |

The IOC configures SSI from PVs `_SSITOTBITS_SP`, `_SSISINGLETBITS_SP`, `_SSIDATA_CMD`, and `_SSIINPUT_CMD`. SSI capability is queried at connection via SIA=? and SIE=?.

### SS

**Configure BiSS** -- Configure BiSS-C absolute encoder.

Syntax: `SSX=p,q,r,s,t`

| Argument | Description |
|----------|-------------|
| p | Data 1 bits |
| q | Data 2 bits |
| r | Zero padding bits |
| s | Clock divider |
| t | Input assignment |

Similar to SI but for the BiSS-C protocol, which supports multi-turn and diagnostic data. The IOC configures BiSS from PVs `_BISSDATA1_SP`, `_BISSDATA2_SP`, `_BISSZP_SP`, `_BISSCD_SP`, and `_BISSINPUT_CMD`. BiSS capability is queried at connection via SSA=? and SSE=?.

### SY

**BiSS Active Level** -- Set the active clock level for BiSS communication.

Syntax: `SYX=n`

`SYA=0` = active low clock. `SYA=1` = active high clock. Must match the encoder's expected clock polarity.

---

## Communication / System

### DR

**Data Record Update Rate** -- Set the interval for data record transmission.

Syntax: `DR n,h`

`DR 8000,2` sets 8ms updates on handle 2. The first argument is the interval in microseconds (minimum 2000 = 2ms). The second argument is the network handle. Set to 0 to disable data record transmission on that handle.

This is the fundamental update rate for the IOC. The `updatePeriod` parameter of `GalilCreateController` maps directly to DR. Async UDP mode uses the controller-pushed data records; synchronous TCP mode polls with QR instead.

### MG

**Message** -- General-purpose query and output command.

Syntax: `MG expression` or `MG _variable`

`MG _MTA` returns the motor type setting for axis A. `MG @AN[0]` reads analog input 0. `MG _BN` returns the controller serial number. MG can query any internal variable (prefixed with `_`) or operand (prefixed with `@`).

The IOC uses MG extensively to read values not available in the data record, including: `_BN` (serial number), `_CN0` (limit type), `_CA` (coordinate system), `_EU0`/`_EU1` (EtherCat status), and motor configuration parameters.

### RV

**Revision** -- Return the controller firmware revision string.

Syntax: `RV` -- returns revision string.

The IOC queries the revision at connection time to populate the `MODEL_MON` PV and determine controller capabilities (SSI, BiSS, PVT, EtherCat) based on the model string.

### TH

**Tell Ethernet Handle** -- Return Ethernet connection information including MAC address.

Syntax: `TH` -- returns handle information.

The response includes the controller's MAC address, IP address, and active connections. The IOC parses the MAC address from the TH response to populate the `ETHADDR_MON` PV.

### ID

**Identify** -- Return the controller model string.

Syntax: `ID` -- returns model identification.

Returns a string like "DMC4143 Rev 1.3a". Closely related to RV; the IOC uses the binary RV query at connection rather than the ASCII ID command.

### BN

**Burn** -- Save current parameter settings to EEPROM.

Syntax: `BN`

Saves servo tuning (KP, KI, KD), limits (ER, TL), speeds (SP, AC, DC), and other axis parameters to non-volatile memory. Does not save program code (use BP) or arrays/variables (use BV). The IOC sends BN when `burnProgram=1` in `GalilStartController`.

### BP

**Burn Program** -- Save the current DMC program to EEPROM.

Syntax: `BP`

Saves the downloaded DMC code to non-volatile memory so it persists across power cycles. Sent by `GalilStartController` when the burn option is enabled.

### RS

**Reset** -- Reset the controller.

Syntax: `RS`

Clears program memory, stops all motion, and restarts the controller. The IOC does not normally issue RS during operation. Used primarily during initial hardware setup.

### TC

**Tell Error Code** -- Return the error code for the most recent command error.

Syntax: `TC n`

`TC0` returns the numeric error code. `TC1` returns a descriptive error string. The IOC reads TC after a failed command to provide diagnostic information in the `ERROR_MON` PV.

Common error codes: 1 = unrecognized command, 2 = wrong command argument, 3 = argument out of range, 6 = command not valid while running.

### CF

**Configure Unsolicited Messages** -- Set which handle receives unsolicited MG output from DMC code.

Syntax: `CF h`

Directs unsolicited messages (generated by MG commands in DMC programs running on the controller) to a specified network handle. The IOC configures this at startup to route on-controller messages to the appropriate connection.

---

## Status / Query

### TA

**Tell Amplifier Error Status** -- Return amplifier error status bits.

Syntax: `TAX` -- returns bit-mapped status word.

| Bit | Meaning |
|-----|---------|
| 0 | EtherCat communication lost (ELO) |
| 1 | Hall error |
| 2 | Overcurrent |
| 3 | Undervoltage |
| 4 | Overvoltage |
| 5 | Overtemperature |

The IOC decodes these into individual amplifier status PVs (`_OVERCURRENT_STATUS`, `_UNDERVOLTAGE_STATUS`, etc.). Values come from the data record during polling.

### TB

**Tell Status Byte** -- Return a summary status byte for the controller.

Syntax: `TB` -- returns status byte value.

Includes bits for: program running, trace mode, command ready, echo enabled. Used for controller-level diagnostics.

### TS

**Tell Switches** -- Return the state of home, forward limit, and reverse limit switches.

Syntax: `TSX` -- returns switch status byte.

Bit-mapped: includes forward limit, reverse limit, home switch, and latch states. The IOC reads switch states from the **data record** at the polling rate and decodes them into the motor record `.MSTA` status word.

### TV

**Tell Velocity** -- Return the current actual velocity.

Syntax: `TVX` -- returns velocity in counts/sec.

The actual measured velocity (from encoder differentiation), not the commanded velocity. Available from the **data record**. The IOC publishes this through the `_VELOCITYRAW_MON` PV.

### TT

**Tell Torque** -- Return the current torque (DAC) output.

Syntax: `TTX` -- returns voltage.

The current DAC output in volts, representing the torque command to the amplifier. Available from the **data record**.

### TE

**Tell Error** -- Return the current position error.

Syntax: `TEX` -- returns error in counts.

Position error = TP - RP (actual minus commanded). Available from the **data record**. Published through the `_ERR_MON` PV.

### SC

**Stop Code** -- Return the reason the last motion stopped.

Syntax: `SCX` -- returns numeric stop code.

| Code | Meaning |
|------|---------|
| 0 | Motion in progress |
| 1 | Commanded stop (ST) |
| 2 | Forward limit |
| 3 | Reverse limit |
| 4 | Abort input |
| 6 | Position error exceeded (ER) |
| 7 | Motor off (MO) |
| 8 | Home/index complete |
| 9 | Selective abort |
| 50 | Contour data not available |
| 99 | Homing error |
| 100 | General motion complete |

The IOC reads SC from the data record to determine motor status and populate the stop-reason information.

### QR

**Query Data Record** -- Request a data record immediately.

Syntax: `QR`

Forces the controller to send a data record in response, rather than waiting for the next DR interval. The IOC's GalilPoller uses QR in synchronous TCP polling mode when async UDP data records are not available.

---

## EtherCat

### EU

**EtherCat Network** -- Enable or disable the EtherCat network interface.

Syntax: `EU n`

`EU1` enables the EtherCat network (optionally with a timeout: `EU1<timeout>`). `EU0` disables it. Available on 50xx series controllers only.

The IOC writes EU from the `ECATNETWORK_CMD` PV. Network status is read back via `MG _EU0`.

### EX

**EtherCat Address** -- Set the EtherCat drive address for an axis.

Syntax: `EXX=n`

`EXA=1` assigns EtherCat drive address 1 to axis A. The IOC sets this from the `_ECATADDR_SP` PV.

### EH

**EtherCat Hunt** -- Refresh the list of available EtherCat drive addresses.

Syntax: `EH`

Scans the EtherCat bus for connected drives. Used during setup to discover available drive addresses before assigning them with EX.

---

## Coordinate System / Trajectory

### CA

**Coordinate Axes** -- Define which axes belong to a coordinate system.

Syntax: `CA ,X` (adds axis X to the active system)

Assigns axes to coordinate system S or T for coordinated (vector) moves. The active system is selected with the internal `_CA` variable. The IOC writes CA from the `COORDSYS_CMD` PV. The current setting is read back via `MG _CA`.

### CS

**Clear Sequence** -- Clear the linear interpolation buffer for a coordinate system.

Syntax: `CSS` or `CST`

Clears the motion segment buffer so new LI or PV segments can be loaded. Must be issued before loading a new set of interpolation segments.

### LI

**Linear Interpolation Distance** -- Specify a segment distance for each axis in the interpolation buffer.

Syntax: `LIA=n,B=m`

`LIA=1000,B=500` moves axis A by 1000 counts and axis B by 500 counts during this segment. All axes in the segment take the same time, so the controller adjusts individual speeds proportionally. Segments are executed in sequence after BG S/T.

### LE

**Linear Interpolation End** -- Mark the end of the interpolation sequence.

Syntax: `LE`

The controller decelerates to a stop after executing this final segment. Must be the last entry in a linear interpolation sequence.

### LM

**Linear Interpolation Mode** -- Define which axes participate in linear interpolation.

Syntax: `LMXY`

`LMAB` assigns axes A and B to the coordinate system for linear interpolation. Axes in the LM group share a common time base during interpolated moves.

### PV

**PVT Data** -- Specify a Position-Velocity-Time data point for PVT trajectory mode.

Syntax: `PVX=p,v,t`

`PVA=1000,500,20` means axis A should be at position 1000 with velocity 500 counts/sec after 20ms. PVT mode provides smoother trajectories than LI by specifying both position and velocity at each point, allowing the controller to compute smooth spline interpolation between points.

Available on Accelera series (40xx) and above.

### BT

**Begin PVT Motion** -- Start execution of loaded PVT trajectory segments.

Syntax: `BT`

Issued after loading PV segments. The controller begins executing the trajectory immediately.

### VS

**Vector Speed** -- Set the vector speed for coordinated motion.

Syntax: `VS n`

Units: counts/sec. This is the resultant speed across all axes in the coordinate system. For example, if axes A and B are both in the system and VS=10000, the vector sum of their individual speeds equals 10000 counts/sec.

### VA

**Vector Acceleration** -- Set the vector acceleration for coordinated motion.

Syntax: `VA n`

Units: counts/sec^2. Acceleration rate for the resultant velocity vector.

### VD

**Vector Deceleration** -- Set the vector deceleration for coordinated motion.

Syntax: `VD n`

Units: counts/sec^2. Deceleration rate for the resultant velocity vector.

---

## Array Commands

### QU

**Upload Array** -- Upload array data from the controller to the host.

Syntax: `QU name[], start, end`

`QU data[]` uploads the entire array named "data". `QU data[], 0, 99` uploads elements 0 through 99. The IOC uses QU when the `UPLOAD_CMD` PV is triggered, uploading all user arrays and populating the `UARRAY` waveform PVs.

### DM

**Dimension Array** -- Create a named array on the controller.

Syntax: `DM name[size]`

`DM data[1000]` creates a 1000-element array named "data". Arrays persist in controller RAM until RS or power cycle (unless saved with BV). Array names can be up to 8 characters.

### BV

**Burn Variables and Arrays** -- Save all variables and arrays to EEPROM.

Syntax: `BV`

Unlike BN (which saves parameters only), BV saves user-created variables and array contents to non-volatile memory. Arrays must be dimensioned with DM before BV will save them.

---

## Error Clear

### AZ

**Clear Latched Amplifier Errors** -- Clear latched amplifier fault flags.

Syntax: `AZ n`

`AZ1` clears faults on all amplifiers. Resets latched overcurrent, overvoltage, undervoltage, overtemperature, and ELO (EtherCat Lost) flags so the amplifier can be re-enabled. Does not fix the underlying condition -- if the fault condition persists, the flag will latch again.

The IOC sends AZ1 when the `CLEARAMPFAULTS_CMD` PV is processed. Available on 40xx/41xx series controllers.
