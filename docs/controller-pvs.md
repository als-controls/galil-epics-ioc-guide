# Controller PVs

Controller-level PVs provide identity information, status monitoring, and global configuration for a Galil controller. They are defined in two templates:

- **galil_dmc_ctrl.template** -- for DMC motion controllers (full feature set)
- **galil_rio_ctrl.template** -- for RIO remote I/O controllers (subset)

All PVs use the prefix `$(P)`, which is typically set to something like `DMC01:` or `RIO01:` in the IOC startup script.

---

## DMC Controller PVs

The DMC template exposes the complete set of controller PVs described in the sections below.

### Controller Identity and Status

These read-only PVs report the controller's hardware identity and communication health.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `DRIVER_MON` | stringin | `CONTROLLER_DRIVER` | -- | Driver version string (set internally by driver) |
| `MODEL_MON` | stringin | `CONTROLLER_MODEL` | [RV](galil-command-reference.md#rv) | Controller model (e.g. "DMC4143"). Queried at connection via binary RV command |
| `ADDRESS_MON` | stringin | `CONTROLLER_ADDRESS` | -- | Controller address (from `GalilCreateController` parameter) |
| `ETHADDR_MON` | stringin | `CONTROLLER_ETHADDR` | [TH](galil-command-reference.md#th) | Ethernet MAC address. Parsed from TH (Tell Ethernet Handle) response |
| `SERIALNUM_MON` | stringin | `CONTROLLER_SERIALNUM` | [MG](galil-command-reference.md#mg) _BN | Controller serial number. Queried via MG _BN (Message, internal variable) |
| `COMMERR_STATUS` | bi | `CONTROLLER_COMMERR` | -- | Communication error. 0 = OK, 1 = Error (MAJOR alarm). Driver-internal status |
| `START_STATUS` | bi | `CONTROLLER_START` | -- | Startup status. 0 = Error (MAJOR alarm), 1 = OK. Driver-internal status |
| `SSICAPABLE_STATUS` | bi | `CONTROLLER_SSICAPABLE` | [SI](galil-command-reference.md#si)A, [SI](galil-command-reference.md#si)E | SSI encoder capable (determined from model string and SIA=?/SIE=? queries) |
| `BISSCAPABLE_STATUS` | bi | `CONTROLLER_BISSCAPABLE` | [SS](galil-command-reference.md#ss)A, [SS](galil-command-reference.md#ss)E | BiSS encoder capable (determined from model string and SSA=?/SSE=? queries) |
| `PVTCAPABLE_STATUS` | bi | `CONTROLLER_PVTCAPABLE` | -- | PVT trajectory capable (determined from model string: 30xx/40xx/50xx series) |
| `ECATCAPABLE_STATUS` | bi | `CONTROLLER_ECATCAPABLE` | -- | EtherCat capable (determined from model string: 50xx series) |

### Switch Configuration

These PVs configure how the controller interprets limit and home switches. They all map to the Galil [`CN`](galil-command-reference.md#cn) (Configure) command.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `LIMITTYPE_CMD` | bo | `CONTROLLER_LIMITTYPE` | [CN](galil-command-reference.md#cn) | Set limit switch type: NO (normally open) or NC (normally closed) |
| `LIMITTYPE_STATUS` | bi | `CONTROLLER_LIMITTYPE` | [MG](galil-command-reference.md#mg) _CN0 | Readback of limit switch type |
| `HOMETYPE_CMD` | bo | `CONTROLLER_HOMETYPE` | [CN](galil-command-reference.md#cn) | Set home switch type: NO or NC |
| `HOMETYPE_STATUS` | bi | `CONTROLLER_HOMETYPE` | [CN](galil-command-reference.md#cn) | Readback of home switch type |
| `HOMEEDGE_CMD` | bo | `CONTROLLER_HOMEEDGE` | [CN](galil-command-reference.md#cn) | Home edge: Reverse or Forward |
| `HOMEEDGE_STATUS` | bi | `CONTROLLER_HOMEEDGE` | [CN](galil-command-reference.md#cn) | Readback of home edge |

### EtherCat

These PVs control and monitor the EtherCat network interface, available on controllers with EtherCat hardware (50xx series).

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `ECATNETWORK_CMD` | bo | `CONTROLLER_ECAT_NETWORK` | [EU](galil-command-reference.md#eu) | Enable (`EU1`) or disable (`EU0`) the EtherCat network |
| `ECATNETWORK_STATUS` | bi | `CONTROLLER_ECAT_NETWORK` | [MG](galil-command-reference.md#mg) _EU0 | EtherCat network status |
| `ECATFLT_STATUS` | ai | `CONTROLLER_ECAT_FAULT` | -- | EtherCat fault code (polled at 1 second) |

### Deferred Moves

Deferred moves allow multiple axes to start simultaneously. When deferred mode is active, move commands are buffered. The Galil [`BG`](galil-command-reference.md#bg) (Begin) command is issued when the mode is set back to Go.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `DEFER_CMD` | bo | `MOTOR_DEFER_MOVES` | [BG](galil-command-reference.md#bg) | Set deferred mode: Go (execute immediately) or Defer (buffer moves). Issues BG (Begin) with axes or coordinate system when set to Go |
| `DEFER_STATUS` | bi | `MOTOR_DEFER_MOVES` | -- | Deferred moves status (driver-internal) |
| `DEFER_MODE_CMD` | bo | `CONTROLLER_DEFERRED_MODE` | -- | Deferred mode: "Sync start only" or "Sync start/stop". When set to "Sync start/stop", the [ST](galil-command-reference.md#st) (Stop) command is also deferred. Driver-internal setting |
| `DEFER_MODE_STATUS` | bi | `CONTROLLER_DEFERRED_MODE` | -- | Deferred mode readback |

### Coordinate System Selection

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `COORDSYS_CMD` | bo | `COORDINATE_SYSTEM` | [CA](galil-command-reference.md#ca) | Select active coordinate system: S or T. Issues CA (Coordinate Axes define) |
| `COORDSYS_STATUS` | bi | `COORDINATE_SYSTEM` | [MG](galil-command-reference.md#mg) _CA | Active coordinate system readback |

### Output Compare

Output compare generates a pulse on a digital output when the encoder crosses specified positions. It uses the Galil [`OC`](galil-command-reference.md#oc) (Output Compare) command. Two output compare channels are available: OC1 covers axes A through D, and OC2 covers axes E through H.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `OC1START_CMD` | ao | `OUTPUT_COMPARE_START` (addr 0) | [OC](galil-command-reference.md#oc) | Output compare 1 start position |
| `OC1START_MON` | ai | `OUTPUT_COMPARE_START` (addr 0) | [OC](galil-command-reference.md#oc) | Readback |
| `OC1INCR_CMD` | ao | `OUTPUT_COMPARE_INCR` (addr 0) | [OC](galil-command-reference.md#oc) | Output compare 1 increment |
| `OC1INCR_MON` | ai | `OUTPUT_COMPARE_INCR` (addr 0) | [OC](galil-command-reference.md#oc) | Readback |
| `OC1AXIS_CMD` | mbbo | `OUTPUT_COMPARE_AXIS` (addr 0) | [OC](galil-command-reference.md#oc) | Output compare 1 axis: OFF, A, B, C, D |
| `OC1AXIS_STATUS` | mbbi | `OUTPUT_COMPARE_AXIS` (addr 0) | [OC](galil-command-reference.md#oc) | Readback |
| `OC2START_CMD` | ao | `OUTPUT_COMPARE_START` (addr 1) | [OC](galil-command-reference.md#oc) | Output compare 2 start position |
| `OC2START_MON` | ai | `OUTPUT_COMPARE_START` (addr 1) | [OC](galil-command-reference.md#oc) | Readback |
| `OC2INCR_CMD` | ao | `OUTPUT_COMPARE_INCR` (addr 1) | [OC](galil-command-reference.md#oc) | Output compare 2 increment |
| `OC2INCR_MON` | ai | `OUTPUT_COMPARE_INCR` (addr 1) | [OC](galil-command-reference.md#oc) | Readback |
| `OC2AXIS_CMD` | mbbo | `OUTPUT_COMPARE_AXIS` (addr 1) | [OC](galil-command-reference.md#oc) | Output compare 2 axis: OFF, E, F, G, H |
| `OC2AXIS_STATUS` | mbbi | `OUTPUT_COMPARE_AXIS` (addr 1) | [OC](galil-command-reference.md#oc) | Readback |
| `OCMESSAGE_MON` | waveform | `OUTPUT_COMPARE_MESSAGE` | -- | Output compare status message (driver-internal) |

### User Array Upload

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `UPLOAD_CMD` | bo | `CONTROLLER_UARRAY_UPLOAD` | [QU](galil-command-reference.md#qu) | Trigger upload of user arrays from controller. Issues QU (Upload Array) for each array |
| `UPLOAD_STATUS` | bi | `CONTROLLER_UARRAY_UPLOAD` | -- | Upload status: Idle or Uploading |

### Error Display

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `ERROR_MON` | waveform | `CONTROLLER_ERROR` | -- | Controller error message (256-character waveform). Driver-internal status buffer |

### Command Console

These PVs provide a pass-through interface for sending arbitrary Galil commands and reading the responses.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `SEND_STR_CMD` | stringout | `USER_OCTET` | (any) | Send any Galil command string to the controller |
| `SEND_STR_MON` | waveform | `USER_OCTET` | (any) | Response string from the last command (256-character waveform) |
| `SEND_STRVAL_MON` | ai | `USER_OCTET_VAL` | (any) | Numeric value parsed from the last command response |

### Amplifier Fault Clear

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `CLEARAMPFAULTS_CMD` | bo | `CONTROLLER_CLEARAMPFAULTS` | [AZ](galil-command-reference.md#az) | Clear amplifier faults. Issues AZ1 (Clear Latched Amplifier Errors, all amps). 40xx/41xx series only |

---

## RIO Controller PVs

The RIO template (`galil_rio_ctrl.template`) provides a subset of the DMC controller PVs, limited to what is relevant for a remote I/O controller without motor axes.

### Included PVs

**Identity:**
`DRIVER_MON`, `MODEL_MON`, `ADDRESS_MON`, `ETHADDR_MON`, `SERIALNUM_MON`

**Status:**
`COMMERR_STATUS`, `START_STATUS`

**User Array Upload:**
`UPLOAD_CMD`, `UPLOAD_STATUS`

**Error Display:**
`ERROR_MON`

**Command Console:**
`SEND_STR_CMD`, `SEND_STR_MON`, `SEND_STRVAL_MON`

These PVs have the same record types, asyn drvInfo strings, Galil commands, and behavior as their DMC counterparts described above.

### Features Not Available on RIO

The following DMC features are not present in the RIO template:

- Switch configuration (`LIMITTYPE`, `HOMETYPE`, `HOMEEDGE`)
- Deferred moves (`DEFER`, `DEFER_MODE`)
- Coordinate system selection (`COORDSYS`)
- Output compare (`OC1*`, `OC2*`, `OCMESSAGE`)
- EtherCat (`ECATNETWORK`, `ECATFLT`)
- Capability flags (`SSICAPABLE`, `BISSCAPABLE`, `PVTCAPABLE`, `ECATCAPABLE`)
- Amplifier fault clear (`CLEARAMPFAULTS`)
