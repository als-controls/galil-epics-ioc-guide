# Digital I/O PVs

The DMC-41x3 has optoisolated digital I/O. Controllers with 1-4 axes provide 8 inputs (bank 0) and 8 outputs. Controllers with 5-8 axes provide an additional 8 inputs (bank 1) and 8 outputs. The IOC uses the Galil `@IN` (Read digital input) command for reads and `SB`/`CB` (Set Bit/Clear Bit) for writes. Values come from the data record.

## Digital Inputs

Template: `galil_digital_in_bit.template`

PV pattern: `$(P)$(R)_STATUS`. Uses `asynUInt32Digital` with bit masks.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `_STATUS` | bi | BINARY_IN | @IN, from data record | Digital input bit state |

Template macros:

| Macro | Description |
|-------|-------------|
| `BYTE` | Hardware byte (0-2) |
| `MASK` | Bit mask for this bit |
| `ZNAM` / `ONAM` | State names for 0 and 1 |
| `ZSV` / `OSV` | Alarm severities for each state |

## Digital Outputs

Template: `galil_digital_out_bit.template`

PV pattern: `$(P)$(R)_`.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `_STATUS` | bi | BINARY_OUTRBV | Data record | Digital output bit readback |
| `_CMD` | bo | BINARY_OUT | SB/CB (Set Bit/Clear Bit) | Set digital output bit |

Template macros:

| Macro | Description |
|-------|-------------|
| `WORD` | Hardware word (0-2) |
| `MASK` | Bit mask for this bit |
| `ZNAM` / `ONAM` | State names for 0 and 1 |
| `ZSV` / `OSV` | Alarm severities for each state |

The bit numbering maps to physical I/O ports on the controller. Bank 0 corresponds to byte 0 in the data record, and so on.
