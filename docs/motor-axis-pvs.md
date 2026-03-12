# Motor Axis PVs

This section documents the PVs associated with each motor axis in the Galil IOC. PV names use the macros `$(P)` (prefix, e.g. `DMC01:`), `$(M)` (motor name, e.g. `A`), and `$(ADDR)` (axis number 0--7).

## Standard Motor Record

The IOC uses the standard EPICS motor record with `DTYP="asynMotor"`. The template loaded depends on the motor record version:

- **galil_motor-v6-10up.template** -- motor record 6.10 and later
- **galil_motor-v6-9down.template** -- motor record 6.9 and earlier

The table below shows how the Galil driver maps motor record fields to Galil commands.

| Motor Record Field | Galil Command | Description |
|---|---|---|
| `.VAL` (move) | [PA](galil-command-reference.md#pa) / [PR](galil-command-reference.md#pr) | Commanded position. Driver selects PA (Position Absolute) or PR (Position Relative) based on move type. |
| `.JOGF` / `.JOGR` | [JG](galil-command-reference.md#jg) | Jog velocity. Issues JG with positive or negative velocity. |
| `.HOMF` / `.HOMR` | [HM](galil-command-reference.md#hm), [FI](galil-command-reference.md#fi), [FE](galil-command-reference.md#fe) | Home sequence. Uses generated DMC code combining Home (HM), Find Index (FI), and Find Edge (FE). |
| `.STOP` | [ST](galil-command-reference.md#st) | Stops axis motion. |
| `.VELO` | [SP](galil-command-reference.md#sp) | Commanded velocity. |
| `.ACCL` | [AC](galil-command-reference.md#ac), [DC](galil-command-reference.md#dc) | Acceleration and deceleration time. The driver calculates counts/s^2 from VELO and ACCL. |
| `.BDST` | [PR](galil-command-reference.md#pr) | Backlash distance. Performed as a separate relative move. |
| `.SET` | [DP](galil-command-reference.md#dp), [DE](galil-command-reference.md#de) | Set position mode. Defines the encoder register (DP) and step count register (DE). |
| `.UEIP` | -- | Use Encoder If Present. When 1, position readback comes from the main encoder ([TP](galil-command-reference.md#tp)). When 0, from the step counter ([TD](galil-command-reference.md#td)). |
| `.MRES` | -- | Motor resolution. Converts between EGU and counts. |
| `.ERES` | -- | Encoder resolution. |
| `.RBV` | [TP](galil-command-reference.md#tp) / [TD](galil-command-reference.md#td) | Position readback. Source depends on UEIP and motor type. Read from the data record, not by issuing a direct command. |
| `.MSTA` | Data record status bits | Motor status word. Parsed from the binary data record status word. |
| `.DVAL` | -- | Dial value. |

Most status readbacks -- position, velocity, limits, moving state -- come from the **data record** rather than individual Galil commands. The poller reads the binary data record blob and updates the asyn parameter list each cycle.

## Motor Extras PVs

These Galil-specific PVs are loaded from **galil_motor_extras.template** and provide configuration and status beyond what the standard motor record offers. The PV pattern is `$(P)$(M)_SUFFIX`.

### Motor Configuration

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_MTRTYPE_CMD` | mbbo | MOTOR_TYPE | [MT](galil-command-reference.md#mt) | Motor type selection: Servo, Rev Servo, HA Stepper, LA Stepper, Rev HA Stepper, Rev LA Stepper, PWM servo, PWM rev servo, EtherCat Position, EtherCat Torque, EtherCat Rev Torque, Servo 2PB, Rev Servo 2PB |
| `_MTRTYPE_STATUS` | mbbi | MOTOR_TYPE | [MT](galil-command-reference.md#mt) | Motor type readback |
| `_BRTYPE_CMD` | mbbo | BRUSH_TYPE | [BR](galil-command-reference.md#br) | Brush type: External Drive (-1), Brushless (0), Brush (1) |
| `_BRTYPE_STATUS` | mbbi | BRUSH_TYPE | [BR](galil-command-reference.md#br) | Brush type readback |
| `_ON_CMD` | mbbo | MOTOR_CLOSED_LOOP | [SH](galil-command-reference.md#sh) / [MO](galil-command-reference.md#mo) | Motor on/off. On issues SH (Servo Here) to enable the servo loop; Off issues MO (Motor Off) to disable it. |
| `_ON_STATUS` | bi | MOTOR_STATUS_POWERED | Data record | Motor power status from the data record |
| `_AXIS_STATUS` | mbbi | MOTOR_AXIS | -- | Axis channel (A--H). Informational only. |

### Encoder Configuration

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_MENCTYPE_CMD` | mbbo | MOTOR_MAIN_ENCODER | [CE](galil-command-reference.md#ce) | Main encoder type: Normal Quadrature, Pulse and Dir, Reverse Quadrature, Rev Pulse and Dir |
| `_MENCTYPE_STATUS` | mbbi | MOTOR_MAIN_ENCODER | [CE](galil-command-reference.md#ce) | Main encoder readback |
| `_AENCTYPE_CMD` | mbbo | MOTOR_AUX_ENCODER | [CE](galil-command-reference.md#ce) | Aux encoder type. Values 0, 4, 8, 12 select the upper bits of the CE command. |
| `_AENCTYPE_STATUS` | mbbi | MOTOR_AUX_ENCODER | [CE](galil-command-reference.md#ce) | Aux encoder readback |
| `_ENC_TOLERANCE_SP` | longout | MOTOR_ENCODER_TOLERANCE | -- | Encoder tolerance in counts for motion and direction detection |
| `_ENC_TOLERANCE_MON` | longin | MOTOR_ENCODER_TOLERANCE | -- | Readback |

### SSI Absolute Encoder

All SSI PVs map to the Galil [SI](galil-command-reference.md#si) command.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_SCONN_STATUS` | bi | MOTOR_SSI_CONNECTED | -- | SSI connection status. Goes to MAJOR alarm when disconnected. |
| `_SSIINPUT_CMD` | mbbo | MOTOR_SSIINPUT | [SI](galil-command-reference.md#si) | SSI input mode: Off, Replace main, Replace aux |
| `_SSIINPUT_STATUS` | mbbi | MOTOR_SSIINPUT | [SI](galil-command-reference.md#si) | SSI input readback |
| `_SSITOTBITS_SP` | ao | MOTOR_SSITOTBITS | [SI](galil-command-reference.md#si) | SSI total bits (default 25) |
| `_SSITOTBITS_MON` | ai | MOTOR_SSITOTBITS | [SI](galil-command-reference.md#si) | Readback |
| `_SSISINGLETBITS_SP` | ao | MOTOR_SSISINGLETBITS | [SI](galil-command-reference.md#si) | SSI single-turn bits (default 13) |
| `_SSISINGLETBITS_MON` | ai | MOTOR_SSISINGLETBITS | [SI](galil-command-reference.md#si) | Readback |
| `_SSIERRBITS_SP` | ao | MOTOR_SSIERRBITS | [SI](galil-command-reference.md#si) | SSI error bits (default 0) |
| `_SSIERRBITS_MON` | ai | MOTOR_SSIERRBITS | [SI](galil-command-reference.md#si) | Readback |
| `_SSITIME_SP` | ao | MOTOR_SSITIME | [SI](galil-command-reference.md#si) | SSI clock rate timecode (default 13) |
| `_SSITIME_MON` | ai | MOTOR_SSITIME | [SI](galil-command-reference.md#si) | Readback |
| `_SSIDATA_CMD` | bo | MOTOR_SSIDATA | [SI](galil-command-reference.md#si) | SSI data format: Binary or Gray |
| `_SSIDATA_STATUS` | bi | MOTOR_SSIDATA | [SI](galil-command-reference.md#si) | Readback |
| `_SSIINVERT_CMD` | bo | MOTOR_SSIINVERT | -- | Invert SSI direction: Off / On |
| `_SSIINVERT_STATUS` | bi | MOTOR_SSIINVERT | -- | Readback |

### BiSS Absolute Encoder

BiSS configuration PVs map to the Galil [SS](galil-command-reference.md#ss) command; the active level PVs use [SY](galil-command-reference.md#sy).

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_BISSINPUT_CMD` | mbbo | MOTOR_BISSINPUT | [SS](galil-command-reference.md#ss) | BiSS input mode: Off, Replace main, Replace aux |
| `_BISSINPUT_STATUS` | mbbi | MOTOR_BISSINPUT | [SS](galil-command-reference.md#ss) | BiSS input readback |
| `_BISSDATA1_SP` | longout | MOTOR_BISSDATA1 | [SS](galil-command-reference.md#ss) | BiSS data bits 1 (-38 to 38, default 27) |
| `_BISSDATA1_MON` | longin | MOTOR_BISSDATA1 | [SS](galil-command-reference.md#ss) | Readback |
| `_BISSDATA2_SP` | longout | MOTOR_BISSDATA2 | [SS](galil-command-reference.md#ss) | BiSS data bits 2 (0 to 38, default 27) |
| `_BISSDATA2_MON` | longin | MOTOR_BISSDATA2 | [SS](galil-command-reference.md#ss) | Readback |
| `_BISSZP_SP` | longout | MOTOR_BISSZP | [SS](galil-command-reference.md#ss) | BiSS zero pad bits (0 to 7) |
| `_BISSZP_MON` | longin | MOTOR_BISSZP | [SS](galil-command-reference.md#ss) | Readback |
| `_BISSCD_SP` | longout | MOTOR_BISSCD | [SS](galil-command-reference.md#ss) | BiSS clock divider (4 to 26, default 14) |
| `_BISSCD_MON` | longin | MOTOR_BISSCD | [SS](galil-command-reference.md#ss) | Readback |
| `_BISSLEVEL_CMD` | mbbo | MOTOR_BISSLEVEL | [SY](galil-command-reference.md#sy) | BiSS active level: Low/Low, Low/High, High/Low, High/High |
| `_BISSLEVEL_STATUS` | mbbi | MOTOR_BISSLEVEL | [SY](galil-command-reference.md#sy) | Readback |
| `_BISSSTAT_TIMEOUT` | bi | MOTOR_BISSSTAT_TIMEOUT | -- | BiSS timeout status (MAJOR alarm when active) |
| `_BISSSTAT_CRC` | bi | MOTOR_BISSSTAT_CRC | -- | BiSS CRC valid/invalid (MAJOR alarm on invalid) |
| `_BISSSTAT_ERROR` | bi | MOTOR_BISSSTAT_ERROR | -- | BiSS error status (MAJOR alarm when active) |
| `_BISSSTAT_WARN` | bi | MOTOR_BISSSTAT_WARN | -- | BiSS warning (MAJOR alarm when active) |
| `_BISSSTAT_POLL_CMD` | bo | MOTOR_BISSSTAT_POLL | -- | Enable or disable BiSS status polling |
| `_BISSSTAT_POLL` | bi | MOTOR_BISSSTAT_POLL | -- | BiSS status poll readback |

### Homing

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_HOMING_STATUS` | bi | MOTOR_HOMING | -- | True while homing is in progress, including any jog-after-home move |
| `_HOMING_MONITOR` | calcout | -- | -- | Combined homing status derived from HOMR, HOMF, and HOMING_STATUS |
| `_HOMR_CMD` | bo | MOTOR_HOMR | Generated home code | Trigger independent home reverse |
| `_HOMF_CMD` | bo | MOTOR_HOMF | Generated home code | Trigger independent home forward |
| `_ULAH_CMD` | bo | MOTOR_USELIMITASHOME | -- | Use limit switch as home: No / Yes |
| `_ULAH_STATUS` | bi | MOTOR_USELIMITASHOME | -- | Readback |
| `_UINDEX_CMD` | bo | MOTOR_USEINDEX | [FI](galil-command-reference.md#fi) | Use index pulse during home: No / Yes |
| `_UINDEX_STATUS` | bi | MOTOR_USEINDEX | [FI](galil-command-reference.md#fi) | Readback |
| `_USWITCH_CMD` | bo | MOTOR_USESWITCH | -- | Use home switch: No / Yes |
| `_USWITCH_STATUS` | bi | MOTOR_USESWITCH | -- | Readback |
| `_JAH_CMD` | bo | MOTOR_JOG_AHOME | [JG](galil-command-reference.md#jg) | Jog after home: No / Yes |
| `_JAH_STATUS` | bi | MOTOR_JOG_AHOME | [JG](galil-command-reference.md#jg) | Readback |
| `_JAHV_SP` | ao | MOTOR_JOG_AHOME_VALUE | [PA](galil-command-reference.md#pa) | Jog-after-home target position in EGU |
| `_JAHV_MON` | ai | MOTOR_JOG_AHOME_VALUE | [PA](galil-command-reference.md#pa) | Readback |
| `_HOMEALLOWED_CMD` | mbbo | MOTOR_HOME_ALLOWED | -- | Permitted home directions: None, Reverse, Forward, Both |
| `_HOMEALLOWED_STATUS` | mbbi | MOTOR_HOME_ALLOWED | -- | Readback |

### Encoder Stall Detection

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_ESTALL_STATUS` | bi | MOTOR_STATUS_SLIP | Data record | Encoder stall detected (MAJOR alarm) |
| `_ESTALLTIME_SP` | ao | MOTOR_ENCODER_STALL_TIME | -- | Time the encoder must be stopped before a stall is declared, in seconds (default 0.2) |
| `_ESTALLTIME_MON` | ai | MOTOR_ENCODER_STALL_TIME | -- | Readback |

### Step Motor Smoothing

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_STEPSMOOTH_SP` | ao | MOTOR_STEPSMOOTH | [KS](galil-command-reference.md#ks) | Smoothing factor for stepper axes (0.25 to 128, default 1.313) |
| `_STEPSMOOTH_MON` | ai | MOTOR_STEPSMOOTH | [KS](galil-command-reference.md#ks) | Readback |

### Servo Error

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_ERRLIMIT_SP` | ao | MOTOR_ERRLIM | [ER](galil-command-reference.md#er) | Position error limit in counts (default 16384) |
| `_ERRLIMIT_MON` | ai | MOTOR_ERRLIM | [ER](galil-command-reference.md#er) | Readback |
| `_ERR_MON` | ai | MOTOR_ERR | Data record (`_TEn`) | Current position error in counts, read from the data record |
| `_OFFONERR_CMD` | mbbo | MOTOR_OOE | [OE](galil-command-reference.md#oe) | Off-on-error mode: Off, Position error, Limits, Both |
| `_OFFONERR_STATUS` | mbbi | MOTOR_OOE | [OE](galil-command-reference.md#oe) | Readback |

### Velocity Readback

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_VELOCITYRAW_MON` | ai | MOTOR_VELOCITY_RAW | Data record (`_TVn`) | Velocity in counts/s from the data record |
| `_VELOCITYEGU_MON` | ai | MOTOR_VELOCITY_EGU | Data record | Velocity in EGU/s, converted from raw using MRES |

### Motor Power Auto On/Off

When enabled, the driver automatically issues [SH](galil-command-reference.md#sh) before a move and [MO](galil-command-reference.md#mo) after the move completes, with configurable delays.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_AUTOONOFF_CMD` | bo | MOTOR_AUTO_ONOFF | [SH](galil-command-reference.md#sh) / [MO](galil-command-reference.md#mo) | Enable auto on/off |
| `_AUTOONOFF_STATUS` | bi | MOTOR_AUTO_ONOFF | -- | Readback |
| `_ONDELAY_SP` | ao | MOTOR_AUTO_ONDELAY | -- | Delay after [SH](galil-command-reference.md#sh) before starting the move (0.001 to 3 s, default 0.2) |
| `_ONDELAY_MON` | ai | MOTOR_AUTO_ONDELAY | -- | Readback |
| `_OFFDELAY_SP` | ao | MOTOR_AUTO_OFFDELAY | -- | Delay after move completes before issuing [MO](galil-command-reference.md#mo) (0.001 to 3 s, default 0.2) |
| `_OFFDELAY_MON` | ai | MOTOR_AUTO_OFFDELAY | -- | Readback |

### Brake Control

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_BRAKE_STATUS` | bi | MOTOR_BRAKE | [SB](galil-command-reference.md#sb) / [CB](galil-command-reference.md#cb) | Brake on/off status. SB (Set Bit) engages; CB (Clear Bit) releases. |
| `_AUTOBRAKE_CMD` | bo | MOTOR_AUTO_BRAKE | [SB](galil-command-reference.md#sb) / [CB](galil-command-reference.md#cb) | Enable automatic brake engagement between moves |
| `_AUTOBRAKE_STATUS` | bi | MOTOR_AUTO_BRAKE | -- | Readback |
| `_BRAKEPORT_SP` | ao | MOTOR_BRAKEPORT | -- | Digital output port for the brake (-1 = disabled, 0 to 16) |
| `_BRAKEPORT_MON` | ai | MOTOR_BRAKEPORT | -- | Readback |
| `_BRAKEONDELAY_SP` | ao | MOTOR_AUTO_BRAKEONDELAY | -- | Delay after move before engaging brake (0.001 to 3 s, default 0.2) |
| `_BRAKEONDELAY_MON` | ai | MOTOR_AUTO_BRAKEONDELAY | -- | Readback |

### Limit Protection

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_WLP_CMD` | bo | MOTOR_WLP | -- | Wrong limit protection: Off / On (default On). Stops the motor if the wrong limit switch activates during travel. |
| `_WLP_STATUS` | bi | MOTOR_WLP | -- | Readback |
| `_WLPSTOP_STATUS` | bi | MOTOR_WLP_STOP | -- | WLP triggered a stop (MAJOR alarm) |
| `_EGUAFTLIMIT_SP` | ao | MOTOR_EGUAFTER_LIMIT | -- | EGU distance to move off a limit (default 0.001) |
| `_EGUAFTLIMIT_MON` | ai | MOTOR_EGUAFTER_LIMIT | -- | Readback |
| `_LIMITDISABLE_CMD` | mbbo | MOTOR_LIMIT_DISABLE | [LD](galil-command-reference.md#ld) | Disable limits: Off, Fwd Disabled, Rev Disabled, Both Disabled |
| `_LIMITDISABLE_STATUS` | mbbi | MOTOR_LIMIT_DISABLE | [LD](galil-command-reference.md#ld) | Readback |
| `_LIMITCONSISTENT_STATUS` | mbbi | MOTOR_LIMIT_CONSISTENT | -- | Limit consistency check: Unknown, Consistent, Not consistent (MAJOR alarm when inconsistent) |

### Amplifier Configuration

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_AMPMODEL_STATUS` | longin | MOTOR_AMP_MODEL | -- | Internal amplifier model number |
| `_AMPGAIN_CMD` | mbbo | MOTOR_AMP_GAIN | [AG](galil-command-reference.md#ag) | Amplifier gain. Enumeration values depend on the amplifier model. |
| `_AMPGAIN_STATUS` | mbbi | MOTOR_AMP_GAIN | [AG](galil-command-reference.md#ag) | Readback |
| `_AMPCLGAIN_CMD` | mbbo | MOTOR_AMP_CURRENTLOOP_GAIN | [AU](galil-command-reference.md#au) | Current loop gain. Enumeration values depend on the amplifier model. |
| `_AMPCLGAIN_STATUS` | mbbi | MOTOR_AMP_CURRENTLOOP_GAIN | [AU](galil-command-reference.md#au) | Readback |
| `_AMPLC_SP` | ao | MOTOR_AMP_LOWCURRENT | [LC](galil-command-reference.md#lc) | Low current stepper mode setting (-32767 to 32767) |
| `_AMPLC_MON` | ai | MOTOR_AMP_LOWCURRENT | [LC](galil-command-reference.md#lc) | Readback |

### Microstep Configuration

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_MICROSTEP_CMD` | mbbo | MOTOR_MICROSTEP | [YA](galil-command-reference.md#ya) | Microsteps per step: 1, 2, 4, 16, 64, 256. Available values depend on the amplifier model. |
| `_MICROSTEP_STATUS` | mbbi | MOTOR_MICROSTEP | [YA](galil-command-reference.md#ya) | Readback |

### EtherCat

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_ECATADDR_SP` | ao | MOTOR_ECAT_ADDR | -- | EtherCat slave address (-255 to 255) |
| `_ECATADDR_MON` | ai | MOTOR_ECAT_ADDR | -- | Readback |
| `_ECATFLT_STATUS` | bi | MOTOR_ECAT_FAULT | -- | EtherCat fault (MAJOR alarm) |
| `_ECATFLTRST_CMD` | bo | MOTOR_ECAT_FAULTRESET | -- | Reset EtherCat fault |

### Amplifier Status

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_HALLERROR_STATUS` | bi | MOTOR_HALLERROR_STATUS | Data record, [TA](galil-command-reference.md#ta) | Hall sensor error (MAJOR alarm) |
| `_ATTORQUELIMIT_STATUS` | bi | MOTOR_ATTORQUELIMIT_STATUS | Data record | At torque limit (MAJOR alarm) |

### Miscellaneous

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|
| `_MCONN_STATUS` | bi | MOTOR_MCONN | -- | Motor connected status. Goes to MINOR alarm when disconnected. |
| `_USERDATA_MON` | ai | MOTOR_USER_DATA | ZA | User-defined data from the data record |
| `_USERDATADEADB_SP` | ao | MOTOR_USER_DATA_DEADB | -- | Deadband for user data updates (default 1) |
| `_STOPDELAY_SP` | ao | MOTOR_STOP_DELAY | -- | Delay before issuing a stop command, in seconds (default 0) |
| `_STOPDELAY_MON` | ai | MOTOR_STOP_DELAY | -- | Readback |
| `_STATUS_POLL_DELAY_CMD` | ao | MOTOR_STATUS_POLL_DELAY | -- | Status poll interval for slow-changing items such as axis and encoder status (0.1 to 10 s, default 1) |
| `_STATUS_POLL_DELAY_MON` | ai | MOTOR_STATUS_POLL_DELAY | -- | Readback |
