# Data Record & Polling

This section explains how the Galil IOC acquires status data from the controller.

## The Galil Data Record

The Galil controller can produce a binary data record containing real-time status of all axes, I/O, and system state. This is configured with the [`DR`](galil-command-reference.md#dr) (Data Record Update Rate) command. The data record contains:

- **Per-axis**: reference position, encoder position, position error, auxiliary position, velocity, torque, analog input, status bits (moving, limits, home, errors, etc.)
- **System**: digital I/O states, analog inputs/outputs, coordinate system status, amplifier status

The data record is a binary blob (up to 768 bytes) parsed by the GalilPoller class using an offset/type map specific to the controller model.

## GalilPoller

The GalilPoller is a dedicated thread that:

1. Receives data records from the controller (async UDP) or polls for them (sync TCP)
2. Parses the binary fields according to the controller's data record map
3. Updates the asyn ParamList with current values
4. Triggers I/O Intr callbacks so EPICS records with `SCAN="I/O Intr"` update

## Async vs Synchronous Mode

### Async UDP (positive updatePeriod)

The [`DR`](galil-command-reference.md#dr) command configures the controller to push data records at the specified rate via UDP. This provides lower latency and is the preferred mode for ethernet connections. The driver opens a separate UDP handle for receiving these records.

### Synchronous TCP (negative updatePeriod)

The driver polls using the [`QR`](galil-command-reference.md#qr) (I/O Data Record) command at the specified rate. This mode is used as a fallback when UDP is unavailable.

## What Comes From the Data Record

Most read-only status PVs get their data from the data record rather than individual Galil commands. This includes:

- Motor position ([TP](galil-command-reference.md#tp)/[TD](galil-command-reference.md#td) equivalent)
- Motor velocity ([TV](galil-command-reference.md#tv) equivalent)
- Position error ([TE](galil-command-reference.md#te) equivalent)
- Torque output ([TT](galil-command-reference.md#tt) equivalent)
- Limit switch status (_LF, _LR operands)
- Home switch status
- Motor moving/stopped status
- Axis stop code
- Digital I/O states
- Analog input values
- Amplifier status ([TA](galil-command-reference.md#ta) bits)

## What Uses Direct Commands

Some PVs send direct Galil commands rather than reading from the data record:

- All write PVs (motor moves, configuration changes)
- Some read PVs that are configuration values rather than real-time status (e.g., motor type readback on initial query)
- The command console (`SEND_STR_CMD`)
- User-defined records

## Update Rate Considerations

The data record update period is set by `GalilCreateController` and determines how fast status PVs update.

| Parameter | Typical Value | Notes |
|-----------|---------------|-------|
| Update period | 8 ms | 125 Hz update rate, typical for ethernet |
| Valid range | 2-200 ms | Controller-enforced limits |

Faster rates increase network bandwidth but give more responsive feedback. PVs with `SCAN="I/O Intr"` update every data record cycle. PVs with explicit scan periods (e.g., `"1 second"`) are rate-limited regardless of the data record rate.
