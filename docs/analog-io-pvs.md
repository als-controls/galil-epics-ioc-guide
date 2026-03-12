# Analog I/O PVs

The DMC-41x3 has 8 analog input ports (12-bit standard, 16-bit optional) and analog outputs on each axis. The IOC uses the Galil `@AN` (Analog Input Query) command for reads and `AO` (Analog Output) for writes. Input values are read from the data record.

## Analog Inputs

Template: `galil_analog_in.template`

PV pattern: `$(P)$(R)_` where `R` is the analog input name. ADDR is 0-7.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `_MON` | ai | ANALOG_IN | @AN (Analog Input), from data record | Analog input value in Volts |
| `Deadb_SP` | ao | ANALOG_IN_DEADB | - | Deadband for update (default 0.002V) |

## Analog Outputs

Template: `galil_analog_out.template`

PV pattern: `$(P)$(R)_`. ADDR is 0-7.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `_SP` | ao | ANALOG_OUT | AO (Analog Output) | Set analog output in Volts |
| `_MON` | ai | ANALOG_OUTRBV | Data record | Analog output readback |
| `Deadb_SP` | ao | ANALOG_OUTRBV_DEADB | - | Deadband for readback update (default 0.002V) |
