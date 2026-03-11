# Motor Axis PVs

This section documents the PVs associated with each motor axis in the Galil IOC. PV names use the macros `$(P)` (prefix, e.g. `DMC01:`), `$(M)` (motor name, e.g. `A`), and `$(ADDR)` (axis number 0--7).

## Standard Motor Record

The IOC uses the standard EPICS motor record with `DTYP="asynMotor"`. The template loaded depends on the motor record version:

- **galil_motor-v6-10up.template** -- motor record 6.10 and later
- **galil_motor-v6-9down.template** -- motor record 6.9 and earlier

The table below shows how the Galil driver maps motor record fields to Galil commands.

| Motor Record Field | Galil Command | Description |
|---|---|---|
| `.VAL` (move) | PA / PR | Commanded position. Driver selects PA (Position Absolute) or PR (Position Relative) based on move type. |
| `.JOGF` / `.JOGR` | JG | Jog velocity. Issues JG with positive or negative velocity. |
| `.HOMF` / `.HOMR` | HM, FI, FE | Home sequence. Uses generated DMC code combining Home (HM), Find Index (FI), and Find Edge (FE). |
| `.STOP` | ST | Stops axis motion. |
| `.VELO` | SP | Commanded velocity. |
| `.ACCL` | AC, DC | Acceleration and deceleration time. The driver calculates counts/s^2 from VELO and ACCL. |
| `.BDST` | PR | Backlash distance. Performed as a separate relative move. |
| `.SET` | DP, DE | Set position mode. Defines the encoder register (DP) and step count register (DE). |
| `.UEIP` | -- | Use Encoder If Present. When 1, position readback comes from the main encoder (TP). When 0, from the step counter (TD). |
| `.MRES` | -- | Motor resolution. Converts between EGU and counts. |
| `.ERES` | -- | Encoder resolution. |
| `.RBV` | TP / TD | Position readback. Source depends on UEIP and motor type. Read from the data record, not by issuing a direct command. |
| `.MSTA` | Data record status bits | Motor status word. Parsed from the binary data record status word. |
| `.DVAL` | -- | Dial value. |

Most status readbacks -- position, velocity, limits, moving state -- come from the **data record** rather than individual Galil commands. The poller reads the binary data record blob and updates the asyn parameter list each cycle.

## Motor Extras PVs

These Galil-specific PVs are loaded from **galil_motor_extras.template** and provide configuration and status beyond what the standard motor record offers. The PV pattern is `$(P)$(M)_SUFFIX`.

### Motor Configuration

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_MTRTYPE_CMD` | mbbo | W | MOTOR_TYPE | MT | Motor type selection: Servo, Rev Servo, HA Stepper, LA Stepper, Rev HA Stepper, Rev LA Stepper, PWM servo, PWM rev servo, EtherCat Position, EtherCat Torque, EtherCat Rev Torque, Servo 2PB, Rev Servo 2PB |
| `_MTRTYPE_STATUS` | mbbi | R | MOTOR_TYPE | MT | Motor type readback |
| `_BRTYPE_CMD` | mbbo | W | BRUSH_TYPE | BR | Brush type: External Drive (-1), Brushless (0), Brush (1) |
| `_BRTYPE_STATUS` | mbbi | R | BRUSH_TYPE | BR | Brush type readback |
| `_ON_CMD` | mbbo | W | MOTOR_CLOSED_LOOP | SH / MO | Motor on/off. On issues SH (Servo Here) to enable the servo loop; Off issues MO (Motor Off) to disable it. |
| `_ON_STATUS` | bi | R | MOTOR_STATUS_POWERED | Data record | Motor power status from the data record |
| `_AXIS_STATUS` | mbbi | R | MOTOR_AXIS | -- | Axis channel (A--H). Informational only. |

### Encoder Configuration

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_MENCTYPE_CMD` | mbbo | W | MOTOR_MAIN_ENCODER | CE | Main encoder type: Normal Quadrature, Pulse and Dir, Reverse Quadrature, Rev Pulse and Dir |
| `_MENCTYPE_STATUS` | mbbi | R | MOTOR_MAIN_ENCODER | CE | Main encoder readback |
| `_AENCTYPE_CMD` | mbbo | W | MOTOR_AUX_ENCODER | CE | Aux encoder type. Values 0, 4, 8, 12 select the upper bits of the CE command. |
| `_AENCTYPE_STATUS` | mbbi | R | MOTOR_AUX_ENCODER | CE | Aux encoder readback |
| `_ENC_TOLERANCE_SP` | longout | W | MOTOR_ENCODER_TOLERANCE | -- | Encoder tolerance in counts for motion and direction detection |
| `_ENC_TOLERANCE_MON` | longin | R | MOTOR_ENCODER_TOLERANCE | -- | Readback |

### SSI Absolute Encoder

All SSI PVs map to the Galil SI command.

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_SCONN_STATUS` | bi | R | MOTOR_SSI_CONNECTED | -- | SSI connection status. Goes to MAJOR alarm when disconnected. |
| `_SSIINPUT_CMD` | mbbo | W | MOTOR_SSIINPUT | SI | SSI input mode: Off, Replace main, Replace aux |
| `_SSIINPUT_STATUS` | mbbi | R | MOTOR_SSIINPUT | SI | SSI input readback |
| `_SSITOTBITS_SP` | ao | W | MOTOR_SSITOTBITS | SI | SSI total bits (default 25) |
| `_SSITOTBITS_MON` | ai | R | MOTOR_SSITOTBITS | SI | Readback |
| `_SSISINGLETBITS_SP` | ao | W | MOTOR_SSISINGLETBITS | SI | SSI single-turn bits (default 13) |
| `_SSISINGLETBITS_MON` | ai | R | MOTOR_SSISINGLETBITS | SI | Readback |
| `_SSIERRBITS_SP` | ao | W | MOTOR_SSIERRBITS | SI | SSI error bits (default 0) |
| `_SSIERRBITS_MON` | ai | R | MOTOR_SSIERRBITS | SI | Readback |
| `_SSITIME_SP` | ao | W | MOTOR_SSITIME | SI | SSI clock rate timecode (default 13) |
| `_SSITIME_MON` | ai | R | MOTOR_SSITIME | SI | Readback |
| `_SSIDATA_CMD` | bo | W | MOTOR_SSIDATA | SI | SSI data format: Binary or Gray |
| `_SSIDATA_STATUS` | bi | R | MOTOR_SSIDATA | SI | Readback |
| `_SSIINVERT_CMD` | bo | W | MOTOR_SSIINVERT | -- | Invert SSI direction: Off / On |
| `_SSIINVERT_STATUS` | bi | R | MOTOR_SSIINVERT | -- | Readback |

### BiSS Absolute Encoder

BiSS configuration PVs map to the Galil SS command; the active level PVs use SY.

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_BISSINPUT_CMD` | mbbo | W | MOTOR_BISSINPUT | SS | BiSS input mode: Off, Replace main, Replace aux |
| `_BISSINPUT_STATUS` | mbbi | R | MOTOR_BISSINPUT | SS | BiSS input readback |
| `_BISSDATA1_SP` | longout | W | MOTOR_BISSDATA1 | SS | BiSS data bits 1 (-38 to 38, default 27) |
| `_BISSDATA1_MON` | longin | R | MOTOR_BISSDATA1 | SS | Readback |
| `_BISSDATA2_SP` | longout | W | MOTOR_BISSDATA2 | SS | BiSS data bits 2 (0 to 38, default 27) |
| `_BISSDATA2_MON` | longin | R | MOTOR_BISSDATA2 | SS | Readback |
| `_BISSZP_SP` | longout | W | MOTOR_BISSZP | SS | BiSS zero pad bits (0 to 7) |
| `_BISSZP_MON` | longin | R | MOTOR_BISSZP | SS | Readback |
| `_BISSCD_SP` | longout | W | MOTOR_BISSCD | SS | BiSS clock divider (4 to 26, default 14) |
| `_BISSCD_MON` | longin | R | MOTOR_BISSCD | SS | Readback |
| `_BISSLEVEL_CMD` | mbbo | W | MOTOR_BISSLEVEL | SY | BiSS active level: Low/Low, Low/High, High/Low, High/High |
| `_BISSLEVEL_STATUS` | mbbi | R | MOTOR_BISSLEVEL | SY | Readback |
| `_BISSSTAT_TIMEOUT` | bi | R | MOTOR_BISSSTAT_TIMEOUT | -- | BiSS timeout status (MAJOR alarm when active) |
| `_BISSSTAT_CRC` | bi | R | MOTOR_BISSSTAT_CRC | -- | BiSS CRC valid/invalid (MAJOR alarm on invalid) |
| `_BISSSTAT_ERROR` | bi | R | MOTOR_BISSSTAT_ERROR | -- | BiSS error status (MAJOR alarm when active) |
| `_BISSSTAT_WARN` | bi | R | MOTOR_BISSSTAT_WARN | -- | BiSS warning (MAJOR alarm when active) |
| `_BISSSTAT_POLL_CMD` | bo | W | MOTOR_BISSSTAT_POLL | -- | Enable or disable BiSS status polling |
| `_BISSSTAT_POLL` | bi | R | MOTOR_BISSSTAT_POLL | -- | BiSS status poll readback |

### Homing

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_HOMING_STATUS` | bi | R | MOTOR_HOMING | -- | True while homing is in progress, including any jog-after-home move |
| `_HOMING_MONITOR` | calcout | R | -- | -- | Combined homing status derived from HOMR, HOMF, and HOMING_STATUS |
| `_HOMR_CMD` | bo | W | MOTOR_HOMR | Generated home code | Trigger independent home reverse |
| `_HOMF_CMD` | bo | W | MOTOR_HOMF | Generated home code | Trigger independent home forward |
| `_ULAH_CMD` | bo | W | MOTOR_USELIMITASHOME | -- | Use limit switch as home: No / Yes |
| `_ULAH_STATUS` | bi | R | MOTOR_USELIMITASHOME | -- | Readback |
| `_UINDEX_CMD` | bo | W | MOTOR_USEINDEX | FI | Use index pulse during home: No / Yes |
| `_UINDEX_STATUS` | bi | R | MOTOR_USEINDEX | FI | Readback |
| `_USWITCH_CMD` | bo | W | MOTOR_USESWITCH | -- | Use home switch: No / Yes |
| `_USWITCH_STATUS` | bi | R | MOTOR_USESWITCH | -- | Readback |
| `_JAH_CMD` | bo | W | MOTOR_JOG_AHOME | JG | Jog after home: No / Yes |
| `_JAH_STATUS` | bi | R | MOTOR_JOG_AHOME | JG | Readback |
| `_JAHV_SP` | ao | W | MOTOR_JOG_AHOME_VALUE | PA | Jog-after-home target position in EGU |
| `_JAHV_MON` | ai | R | MOTOR_JOG_AHOME_VALUE | PA | Readback |
| `_HOMEALLOWED_CMD` | mbbo | W | MOTOR_HOME_ALLOWED | -- | Permitted home directions: None, Reverse, Forward, Both |
| `_HOMEALLOWED_STATUS` | mbbi | R | MOTOR_HOME_ALLOWED | -- | Readback |

### Encoder Stall Detection

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_ESTALL_STATUS` | bi | R | MOTOR_STATUS_SLIP | Data record | Encoder stall detected (MAJOR alarm) |
| `_ESTALLTIME_SP` | ao | W | MOTOR_ENCODER_STALL_TIME | -- | Time the encoder must be stopped before a stall is declared, in seconds (default 0.2) |
| `_ESTALLTIME_MON` | ai | R | MOTOR_ENCODER_STALL_TIME | -- | Readback |

### Step Motor Smoothing

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_STEPSMOOTH_SP` | ao | W | MOTOR_STEPSMOOTH | KS | Smoothing factor for stepper axes (0.25 to 128, default 1.313) |
| `_STEPSMOOTH_MON` | ai | R | MOTOR_STEPSMOOTH | KS | Readback |

### Servo Error

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_ERRLIMIT_SP` | ao | W | MOTOR_ERRLIM | ER | Position error limit in counts (default 16384) |
| `_ERRLIMIT_MON` | ai | R | MOTOR_ERRLIM | ER | Readback |
| `_ERR_MON` | ai | R | MOTOR_ERR | Data record (`_TEn`) | Current position error in counts, read from the data record |
| `_OFFONERR_CMD` | mbbo | W | MOTOR_OOE | OE | Off-on-error mode: Off, Position error, Limits, Both |
| `_OFFONERR_STATUS` | mbbi | R | MOTOR_OOE | OE | Readback |

### Velocity Readback

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_VELOCITYRAW_MON` | ai | R | MOTOR_VELOCITY_RAW | Data record (`_TVn`) | Velocity in counts/s from the data record |
| `_VELOCITYEGU_MON` | ai | R | MOTOR_VELOCITY_EGU | Data record | Velocity in EGU/s, converted from raw using MRES |

### Motor Power Auto On/Off

When enabled, the driver automatically issues SH before a move and MO after the move completes, with configurable delays.

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_AUTOONOFF_CMD` | bo | W | MOTOR_AUTO_ONOFF | SH / MO | Enable auto on/off |
| `_AUTOONOFF_STATUS` | bi | R | MOTOR_AUTO_ONOFF | -- | Readback |
| `_ONDELAY_SP` | ao | W | MOTOR_AUTO_ONDELAY | -- | Delay after SH before starting the move (0.001 to 3 s, default 0.2) |
| `_ONDELAY_MON` | ai | R | MOTOR_AUTO_ONDELAY | -- | Readback |
| `_OFFDELAY_SP` | ao | W | MOTOR_AUTO_OFFDELAY | -- | Delay after move completes before issuing MO (0.001 to 3 s, default 0.2) |
| `_OFFDELAY_MON` | ai | R | MOTOR_AUTO_OFFDELAY | -- | Readback |

### Brake Control

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_BRAKE_STATUS` | bi | R | MOTOR_BRAKE | SB / CB | Brake on/off status. SB (Set Bit) engages; CB (Clear Bit) releases. |
| `_AUTOBRAKE_CMD` | bo | W | MOTOR_AUTO_BRAKE | SB / CB | Enable automatic brake engagement between moves |
| `_AUTOBRAKE_STATUS` | bi | R | MOTOR_AUTO_BRAKE | -- | Readback |
| `_BRAKEPORT_SP` | ao | W | MOTOR_BRAKEPORT | -- | Digital output port for the brake (-1 = disabled, 0 to 16) |
| `_BRAKEPORT_MON` | ai | R | MOTOR_BRAKEPORT | -- | Readback |
| `_BRAKEONDELAY_SP` | ao | W | MOTOR_AUTO_BRAKEONDELAY | -- | Delay after move before engaging brake (0.001 to 3 s, default 0.2) |
| `_BRAKEONDELAY_MON` | ai | R | MOTOR_AUTO_BRAKEONDELAY | -- | Readback |

### Limit Protection

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_WLP_CMD` | bo | W | MOTOR_WLP | -- | Wrong limit protection: Off / On (default On). Stops the motor if the wrong limit switch activates during travel. |
| `_WLP_STATUS` | bi | R | MOTOR_WLP | -- | Readback |
| `_WLPSTOP_STATUS` | bi | R | MOTOR_WLP_STOP | -- | WLP triggered a stop (MAJOR alarm) |
| `_EGUAFTLIMIT_SP` | ao | W | MOTOR_EGUAFTER_LIMIT | -- | EGU distance to move off a limit (default 0.001) |
| `_EGUAFTLIMIT_MON` | ai | R | MOTOR_EGUAFTER_LIMIT | -- | Readback |
| `_LIMITDISABLE_CMD` | mbbo | W | MOTOR_LIMIT_DISABLE | LD | Disable limits: Off, Fwd Disabled, Rev Disabled, Both Disabled |
| `_LIMITDISABLE_STATUS` | mbbi | R | MOTOR_LIMIT_DISABLE | LD | Readback |
| `_LIMITCONSISTENT_STATUS` | mbbi | R | MOTOR_LIMIT_CONSISTENT | -- | Limit consistency check: Unknown, Consistent, Not consistent (MAJOR alarm when inconsistent) |

### Amplifier Configuration

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_AMPMODEL_STATUS` | longin | R | MOTOR_AMP_MODEL | -- | Internal amplifier model number |
| `_AMPGAIN_CMD` | mbbo | W | MOTOR_AMP_GAIN | AG | Amplifier gain. Enumeration values depend on the amplifier model. |
| `_AMPGAIN_STATUS` | mbbi | R | MOTOR_AMP_GAIN | AG | Readback |
| `_AMPCLGAIN_CMD` | mbbo | W | MOTOR_AMP_CURRENTLOOP_GAIN | AU | Current loop gain. Enumeration values depend on the amplifier model. |
| `_AMPCLGAIN_STATUS` | mbbi | R | MOTOR_AMP_CURRENTLOOP_GAIN | AU | Readback |
| `_AMPLC_SP` | ao | W | MOTOR_AMP_LOWCURRENT | LC | Low current stepper mode setting (-32767 to 32767) |
| `_AMPLC_MON` | ai | R | MOTOR_AMP_LOWCURRENT | LC | Readback |

### Microstep Configuration

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_MICROSTEP_CMD` | mbbo | W | MOTOR_MICROSTEP | YA | Microsteps per step: 1, 2, 4, 16, 64, 256. Available values depend on the amplifier model. |
| `_MICROSTEP_STATUS` | mbbi | R | MOTOR_MICROSTEP | YA | Readback |

### EtherCat

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_ECATADDR_SP` | ao | W | MOTOR_ECAT_ADDR | -- | EtherCat slave address (-255 to 255) |
| `_ECATADDR_MON` | ai | R | MOTOR_ECAT_ADDR | -- | Readback |
| `_ECATFLT_STATUS` | bi | R | MOTOR_ECAT_FAULT | -- | EtherCat fault (MAJOR alarm) |
| `_ECATFLTRST_CMD` | bo | W | MOTOR_ECAT_FAULTRESET | -- | Reset EtherCat fault |

### Amplifier Status

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_HALLERROR_STATUS` | bi | R | MOTOR_HALLERROR_STATUS | Data record, TA | Hall sensor error (MAJOR alarm) |
| `_ATTORQUELIMIT_STATUS` | bi | R | MOTOR_ATTORQUELIMIT_STATUS | Data record | At torque limit (MAJOR alarm) |

### Miscellaneous

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|---|---|---|---|---|---|
| `_MCONN_STATUS` | bi | R | MOTOR_MCONN | -- | Motor connected status. Goes to MINOR alarm when disconnected. |
| `_USERDATA_MON` | ai | R | MOTOR_USER_DATA | ZA | User-defined data from the data record |
| `_USERDATADEADB_SP` | ao | W | MOTOR_USER_DATA_DEADB | -- | Deadband for user data updates (default 1) |
| `_STOPDELAY_SP` | ao | W | MOTOR_STOP_DELAY | -- | Delay before issuing a stop command, in seconds (default 0) |
| `_STOPDELAY_MON` | ai | R | MOTOR_STOP_DELAY | -- | Readback |
| `_STATUS_POLL_DELAY_CMD` | ao | W | MOTOR_STATUS_POLL_DELAY | -- | Status poll interval for slow-changing items such as axis and encoder status (0.1 to 10 s, default 1) |
| `_STATUS_POLL_DELAY_MON` | ai | R | MOTOR_STATUS_POLL_DELAY | -- | Readback |
