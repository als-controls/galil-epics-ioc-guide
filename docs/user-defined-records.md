# User-Defined Records & Arrays

## User-Defined Records

Template: `galil_userdef_records.template`

User-defined records allow creating custom PVs that send arbitrary Galil commands or read/write controller variables. They are configured via substitutions files. The `DRVCMD` macro selects between two operating modes:

- **CMD mode** (`DRVCMD=CMD`): `GALILSTR` is a Galil command string. The SP sends the command and MONITOR reads the response.
- **VAR mode** (`DRVCMD=VAR`): `GALILSTR` is a variable name. The SP writes the variable and MONITOR reads it.

PV pattern: `$(P)$(EPICSNAME)_`

| PV Suffix | Record Type | asyn drvInfo | Description |
|-----------|-------------|--------------|-------------|
| `_MONITOR` | ai | USER_CMD or USER_VAR + GALILSTR | Read command response or variable value |
| `_STATUS` | bi | - | Bit 0 of MONITOR value as binary status |
| `_SP` | ao | USER_CMD or USER_VAR + GALILSTR | Send command or set variable |
| `_CMD` | bo | - | Trigger that writes to SP and processes |

### Template Macros

| Macro | Description |
|-------|-------------|
| `EPICSNAME` | PV name component |
| `DRVCMD` | Mode selection: CMD or VAR |
| `GALILSTR` | The Galil command string or variable name |
| `SCAN` | Scan rate for MONITOR |
| `PINI` | Process at init |
| `EGU` | Engineering units |
| `DEFAULT` | Default value for SP |
| `DRVH` / `DRVL` | Drive high/low limits |
| `LOW` / `LSV` | Low alarm threshold and severity |
| `HIGH` / `HSV` | High alarm threshold and severity |
| `ZNAM` / `ONAM` | Binary state names for STATUS |
| `ZSV` / `OSV` | Alarm severities for STATUS states |

### Examples

Reading analog input 1 as a custom record:

```
DRVCMD=CMD, GALILSTR="MG @AN[1]"
```

This queries analog input 1 via the [MG](galil-command-reference.md#mg) (Message) command.

Reading and writing a controller variable:

```
DRVCMD=VAR, GALILSTR="myvar"
```

This reads and writes the variable `myvar` on the controller.

## User Arrays

Template: `galil_user_array.template`

User arrays allow uploading arrays from the controller for monitoring. The driver uses the Galil [`QU`](galil-command-reference.md#qu) (Upload Array) command.

PV pattern: `$(P)UARRAY$(ADDR)_` where `ADDR` is the array index.

| PV Suffix | Record Type | asyn drvInfo | Description |
|-----------|-------------|--------------|-------------|
| `UARRAY$(ADDR)_MON` | waveform (DOUBLE) | CONTROLLER_UARRAY | Array data from controller |
| `UARRAYNAME$(ADDR)_SP` | stringout | CONTROLLER_UARRAY_NAME | Name of controller array to upload |

Use the controller-level `UPLOAD_CMD` PV to trigger the upload after setting the array name.
