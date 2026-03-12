# Cross Reference

Two-way lookup between EPICS PV suffixes and Galil commands. Use your browser's find (Ctrl+F) to search.

"DR" in the Galil Cmd column means the value comes from the [data record](data-record-polling.md), not a direct command.

---

## PVs by Suffix → Galil Commands

All PV suffixes in alphabetical order. Prefix is `$(P)` for controller PVs, `$(P)$(M)` for motor/axis PVs, or as noted.

| PV Suffix | Galil Cmd | Documented In |
|-----------|-----------|---------------|
| `ADDRESS_MON` | — | [Controller PVs](controller-pvs.md) |
| `BISSCAPABLE_STATUS` | — | [Controller PVs](controller-pvs.md) |
| `CalculatedPositions` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `CLEARAMPFAULTS_CMD` | [AZ](galil-command-reference.md#error-clear) | [Controller PVs](controller-pvs.md) |
| `COMMERR_STATUS` | — | [Controller PVs](controller-pvs.md) |
| `COORDSYS_CMD` | [CS](galil-command-reference.md#coordinate-system--trajectory) | [Controller PVs](controller-pvs.md) |
| `COORDSYS_STATUS` | [CS](galil-command-reference.md#coordinate-system--trajectory) | [Controller PVs](controller-pvs.md) |
| `DEFER_CMD` | [BG](galil-command-reference.md#motion-commands) | [Controller PVs](controller-pvs.md) |
| `DEFER_MODE_CMD` | [ST](galil-command-reference.md#motion-commands) | [Controller PVs](controller-pvs.md) |
| `DEFER_MODE_STATUS` | [ST](galil-command-reference.md#motion-commands) | [Controller PVs](controller-pvs.md) |
| `DEFER_STATUS` | [BG](galil-command-reference.md#motion-commands) | [Controller PVs](controller-pvs.md) |
| `DRIVER_MON` | — | [Controller PVs](controller-pvs.md) |
| `ECATCAPABLE_STATUS` | — | [Controller PVs](controller-pvs.md) |
| `ECATFLT_STATUS` | — | [Controller PVs](controller-pvs.md) |
| `ECATNETWORK_CMD` | — | [Controller PVs](controller-pvs.md) |
| `ECATNETWORK_STATUS` | — | [Controller PVs](controller-pvs.md) |
| `ERROR_MON` | — | [Controller PVs](controller-pvs.md) |
| `ETHADDR_MON` | — | [Controller PVs](controller-pvs.md) |
| `HOMEEDGE_CMD` | [CN](galil-command-reference.md#limit-and-home-switches) | [Controller PVs](controller-pvs.md) |
| `HOMEEDGE_STATUS` | [CN](galil-command-reference.md#limit-and-home-switches) | [Controller PVs](controller-pvs.md) |
| `HOMETYPE_CMD` | [CN](galil-command-reference.md#limit-and-home-switches) | [Controller PVs](controller-pvs.md) |
| `HOMETYPE_STATUS` | [CN](galil-command-reference.md#limit-and-home-switches) | [Controller PVs](controller-pvs.md) |
| `LIMITTYPE_CMD` | [CN](galil-command-reference.md#limit-and-home-switches) | [Controller PVs](controller-pvs.md) |
| `LIMITTYPE_STATUS` | [CN](galil-command-reference.md#limit-and-home-switches) | [Controller PVs](controller-pvs.md) |
| `MaxAcceleration` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `MaxPosition` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `MaxVelocity` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `MinPosition` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `MODEL_MON` | [ID](galil-command-reference.md#communication--system) | [Controller PVs](controller-pvs.md) |
| `MoveMode` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `OC1AXIS_CMD` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC1AXIS_STATUS` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC1INCR_CMD` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC1INCR_MON` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC1START_CMD` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC1START_MON` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC2AXIS_CMD` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC2AXIS_STATUS` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC2INCR_CMD` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC2INCR_MON` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC2START_CMD` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OC2START_MON` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `OCMESSAGE_MON` | [OC](galil-command-reference.md#io-commands) | [Controller PVs](controller-pvs.md) |
| `PROFILETYPE_CMD` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `PROFILETYPE_STATUS` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `PVTCAPABLE_STATUS` | — | [Controller PVs](controller-pvs.md) |
| `SEND_STR_CMD` | (any) | [Controller PVs](controller-pvs.md) |
| `SEND_STR_MON` | (any) | [Controller PVs](controller-pvs.md) |
| `SEND_STRVAL_MON` | (any) | [Controller PVs](controller-pvs.md) |
| `SERIALNUM_MON` | — | [Controller PVs](controller-pvs.md) |
| `SSICAPABLE_STATUS` | — | [Controller PVs](controller-pvs.md) |
| `START_STATUS` | — | [Controller PVs](controller-pvs.md) |
| `TrajectoryFile` | — | [Profile/Trajectory PVs](profile-trajectory-pvs.md) |
| `UARRAY$(ADDR)_MON` | [QU](galil-command-reference.md#array-commands) | [User-Defined Records](user-defined-records.md) |
| `UARRAYNAME$(ADDR)_SP` | [QU](galil-command-reference.md#array-commands) | [User-Defined Records](user-defined-records.md) |
| `UPLOAD_CMD` | [QU](galil-command-reference.md#array-commands) | [Controller PVs](controller-pvs.md) |
| `UPLOAD_STATUS` | [QU](galil-command-reference.md#array-commands) | [Controller PVs](controller-pvs.md) |
| `_AENCTYPE_CMD` | [CE](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AENCTYPE_STATUS` | [CE](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AMPCLGAIN_CMD` | [AU](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AMPCLGAIN_STATUS` | [AU](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AMPGAIN_CMD` | [AG](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AMPGAIN_STATUS` | [AG](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AMPLC_MON` | [LC](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AMPLC_SP` | [LC](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AMPMODEL_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ATTORQUELIMIT_STATUS` | DR | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AUTOBRAKE_CMD` | [SB](galil-command-reference.md#io-commands)/[CB](galil-command-reference.md#io-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AUTOBRAKE_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AUTOONOFF_CMD` | [SH](galil-command-reference.md#motor-enabledisable)/[MO](galil-command-reference.md#motor-enabledisable) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AUTOONOFF_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_AXIS_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSCAPABLE_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSCD_MON` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSCD_SP` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSDATA1_MON` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSDATA1_SP` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSDATA2_MON` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSDATA2_SP` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSINPUT_CMD` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSINPUT_STATUS` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSLEVEL_CMD` | [SY](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSLEVEL_STATUS` | [SY](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSSTAT_CRC` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSSTAT_ERROR` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSSTAT_POLL` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSSTAT_POLL_CMD` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSSTAT_TIMEOUT` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSSTAT_WARN` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSZP_MON` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BISSZP_SP` | [SS](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BRAKE_STATUS` | [SB](galil-command-reference.md#io-commands)/[CB](galil-command-reference.md#io-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BRAKEPORT_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BRAKEPORT_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BRAKEONDELAY_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BRAKEONDELAY_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BRTYPE_CMD` | [BR](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_BRTYPE_STATUS` | [BR](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_CMD` (Analog Out) | [AO](galil-command-reference.md#io-commands) | [Analog I/O PVs](analog-io-pvs.md) |
| `_CMD` (Digital Out) | [SB](galil-command-reference.md#io-commands)/[CB](galil-command-reference.md#io-commands) | [Digital I/O PVs](digital-io-pvs.md) |
| `_CMD` (User-Defined) | (any) | [User-Defined Records](user-defined-records.md) |
| `_Deadb_SP` (Analog In) | — | [Analog I/O PVs](analog-io-pvs.md) |
| `_Deadb_SP` (Analog Out) | — | [Analog I/O PVs](analog-io-pvs.md) |
| `_ECATADDR_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ECATADDR_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ECATFLT_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ECATFLTRST_CMD` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ELO_STATUS` | [TA](galil-command-reference.md#status--query) / DR | [Amplifier Status PVs](amplifier-status-pvs.md) |
| `_ENC_TOLERANCE_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ENC_TOLERANCE_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ERRLIMIT_MON` | [ER](galil-command-reference.md#pid--servo-tuning) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ERRLIMIT_SP` | [ER](galil-command-reference.md#pid--servo-tuning) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ERR_MON` | DR | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ESTALL_STATUS` | DR | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ESTALLTIME_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ESTALLTIME_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_FTRANSFORM_SP` | — | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_GO_CMD` | [BG](galil-command-reference.md#motion-commands) | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_HALLERROR_STATUS` | [TA](galil-command-reference.md#status--query) / DR | [Motor Axis PVs](motor-axis-pvs.md) |
| `_HOMEALLOWED_CMD` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_HOMEALLOWED_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_HOMF_CMD` | [HM](galil-command-reference.md#motion-commands)/[FI](galil-command-reference.md#motion-commands)/[FE](galil-command-reference.md#motion-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_HOMING_MONITOR` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_HOMING_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_HOMR_CMD` | [HM](galil-command-reference.md#motion-commands)/[FI](galil-command-reference.md#motion-commands)/[FE](galil-command-reference.md#motion-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_JAH_CMD` | [JG](galil-command-reference.md#motion-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_JAH_STATUS` | [JG](galil-command-reference.md#motion-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_JAHV_MON` | [PA](galil-command-reference.md#motion-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_JAHV_SP` | [PA](galil-command-reference.md#motion-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_LIMITCONSISTENT_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_LIMITDISABLE_CMD` | [LD](galil-command-reference.md#limit-and-home-switches) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_LIMITDISABLE_STATUS` | [LD](galil-command-reference.md#limit-and-home-switches) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_MCONN_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_MENCTYPE_CMD` | [CE](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_MENCTYPE_STATUS` | [CE](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_MICROSTEP_CMD` | [YA](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_MICROSTEP_STATUS` | [YA](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_MON` (Analog In) | [DR / @AN](galil-command-reference.md#io-commands) | [Analog I/O PVs](analog-io-pvs.md) |
| `_MON` (Analog Out) | DR | [Analog I/O PVs](analog-io-pvs.md) |
| `_MONITOR` (User-Defined) | (any) | [User-Defined Records](user-defined-records.md) |
| `_MOTORS_MON` | — | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_MOVN_STATUS` | — | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_MTRTYPE_CMD` | [MT](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_MTRTYPE_STATUS` | [MT](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_NAME_MON` | — | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_OFFDELAY_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_OFFDELAY_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_OFFONERR_CMD` | [OE](galil-command-reference.md#pid--servo-tuning) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_OFFONERR_STATUS` | [OE](galil-command-reference.md#pid--servo-tuning) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ON_CMD` | [SH](galil-command-reference.md#motor-enabledisable)/[MO](galil-command-reference.md#motor-enabledisable) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ON_STATUS` | DR | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ONDELAY_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ONDELAY_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_OVERCURRENT_STATUS` | [TA](galil-command-reference.md#status--query) / DR | [Amplifier Status PVs](amplifier-status-pvs.md) |
| `_OVERTEMPERATURE_STATUS` | [TA](galil-command-reference.md#status--query) / DR | [Amplifier Status PVs](amplifier-status-pvs.md) |
| `_OVERVOLTAGE_STATUS` | [TA](galil-command-reference.md#status--query) / DR | [Amplifier Status PVs](amplifier-status-pvs.md) |
| `_RTRANSFORMA_SP` … `_RTRANSFORMH_SP` | — | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_SCONN_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SEGMENTS_MON` | — | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_SP` (Analog Out) | [AO](galil-command-reference.md#io-commands) | [Analog I/O PVs](analog-io-pvs.md) |
| `_SP` (User-Defined) | (any) | [User-Defined Records](user-defined-records.md) |
| `_SP_MON` (CS Motor) | — | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_SSICAPABLE_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSIDATA_CMD` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSIDATA_STATUS` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSIERRBITS_MON` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSIERRBITS_SP` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSIINPUT_CMD` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSIINPUT_STATUS` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSIINVERT_CMD` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSIINVERT_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSISINGLETBITS_MON` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSISINGLETBITS_SP` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSITIME_MON` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSITIME_SP` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSITOTBITS_MON` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_SSITOTBITS_SP` | [SI](galil-command-reference.md#absolute-encoders) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_STATUS` (Digital In) | [DR / @IN](galil-command-reference.md#io-commands) | [Digital I/O PVs](digital-io-pvs.md) |
| `_STATUS` (Digital Out) | DR | [Digital I/O PVs](digital-io-pvs.md) |
| `_STATUS` (User-Defined) | — | [User-Defined Records](user-defined-records.md) |
| `_STATUS_POLL_DELAY_CMD` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_STATUS_POLL_DELAY_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_STEPSMOOTH_MON` | [KS](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_STEPSMOOTH_SP` | [KS](galil-command-reference.md#motor-configuration) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_STOP_CMD` | [ST](galil-command-reference.md#motion-commands) | [Coordinate System PVs](coordinate-system-pvs.md) |
| `_STOPDELAY_MON` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_STOPDELAY_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_UINDEX_CMD` | [FI](galil-command-reference.md#motion-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_UINDEX_STATUS` | [FI](galil-command-reference.md#motion-commands) | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ULAH_CMD` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_ULAH_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_UNDERVOLTAGE_STATUS` | [TA](galil-command-reference.md#status--query) / DR | [Amplifier Status PVs](amplifier-status-pvs.md) |
| `_USERDATA_MON` | ZA | [Motor Axis PVs](motor-axis-pvs.md) |
| `_USERDATADEADB_SP` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_USWITCH_CMD` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_USWITCH_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_VELOCITYEGU_MON` | DR | [Motor Axis PVs](motor-axis-pvs.md) |
| `_VELOCITYRAW_MON` | DR | [Motor Axis PVs](motor-axis-pvs.md) |
| `_WLP_CMD` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_WLP_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |
| `_WLPSTOP_STATUS` | — | [Motor Axis PVs](motor-axis-pvs.md) |

---

## Galil Commands → PVs

All Galil 2-letter commands in alphabetical order, with the PVs that use them.

| Galil Cmd | PV Suffixes | Documented In |
|-----------|-------------|---------------|
| [AG](galil-command-reference.md#motor-configuration) | `_AMPGAIN_CMD`, `_AMPGAIN_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [@AN](galil-command-reference.md#io-commands) | `_MON` (Analog In) | [Analog I/O PVs](analog-io-pvs.md) |
| [AO](galil-command-reference.md#io-commands) | `_SP` (Analog Out) | [Analog I/O PVs](analog-io-pvs.md) |
| [AU](galil-command-reference.md#motor-configuration) | `_AMPCLGAIN_CMD`, `_AMPCLGAIN_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [AZ](galil-command-reference.md#error-clear) | `CLEARAMPFAULTS_CMD` | [Controller PVs](controller-pvs.md) |
| [BG](galil-command-reference.md#motion-commands) | `DEFER_CMD`, `DEFER_STATUS`, `_GO_CMD` | [Controller PVs](controller-pvs.md), [Coordinate System PVs](coordinate-system-pvs.md) |
| [BR](galil-command-reference.md#motor-configuration) | `_BRTYPE_CMD`, `_BRTYPE_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [CB](galil-command-reference.md#io-commands) | `_AUTOBRAKE_CMD`, `_BRAKE_STATUS`, `_CMD` (Digital Out) | [Motor Axis PVs](motor-axis-pvs.md), [Digital I/O PVs](digital-io-pvs.md) |
| [CE](galil-command-reference.md#motor-configuration) | `_MENCTYPE_CMD`, `_MENCTYPE_STATUS`, `_AENCTYPE_CMD`, `_AENCTYPE_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [CN](galil-command-reference.md#limit-and-home-switches) | `LIMITTYPE_CMD`, `LIMITTYPE_STATUS`, `HOMETYPE_CMD`, `HOMETYPE_STATUS`, `HOMEEDGE_CMD`, `HOMEEDGE_STATUS` | [Controller PVs](controller-pvs.md) |
| [CS](galil-command-reference.md#coordinate-system--trajectory) | `COORDSYS_CMD`, `COORDSYS_STATUS` | [Controller PVs](controller-pvs.md) |
| [ER](galil-command-reference.md#pid--servo-tuning) | `_ERRLIMIT_SP`, `_ERRLIMIT_MON` | [Motor Axis PVs](motor-axis-pvs.md) |
| [FE](galil-command-reference.md#motion-commands) | `_HOMF_CMD`, `_HOMR_CMD` (via generated home code) | [Motor Axis PVs](motor-axis-pvs.md) |
| [FI](galil-command-reference.md#motion-commands) | `_UINDEX_CMD`, `_UINDEX_STATUS`, `_HOMF_CMD`, `_HOMR_CMD` | [Motor Axis PVs](motor-axis-pvs.md) |
| [HM](galil-command-reference.md#motion-commands) | `_HOMF_CMD`, `_HOMR_CMD` (via generated home code) | [Motor Axis PVs](motor-axis-pvs.md) |
| [ID](galil-command-reference.md#communication--system) | `MODEL_MON` | [Controller PVs](controller-pvs.md) |
| [@IN](galil-command-reference.md#io-commands) | `_STATUS` (Digital In) | [Digital I/O PVs](digital-io-pvs.md) |
| [JG](galil-command-reference.md#motion-commands) | `_JAH_CMD`, `_JAH_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [KS](galil-command-reference.md#motor-configuration) | `_STEPSMOOTH_SP`, `_STEPSMOOTH_MON` | [Motor Axis PVs](motor-axis-pvs.md) |
| [LC](galil-command-reference.md#motor-configuration) | `_AMPLC_SP`, `_AMPLC_MON` | [Motor Axis PVs](motor-axis-pvs.md) |
| [LD](galil-command-reference.md#limit-and-home-switches) | `_LIMITDISABLE_CMD`, `_LIMITDISABLE_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [MO](galil-command-reference.md#motor-enabledisable) | `_ON_CMD`, `_AUTOONOFF_CMD` | [Motor Axis PVs](motor-axis-pvs.md) |
| [MT](galil-command-reference.md#motor-configuration) | `_MTRTYPE_CMD`, `_MTRTYPE_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [OC](galil-command-reference.md#io-commands) | `OC1AXIS_CMD`, `OC1AXIS_STATUS`, `OC1INCR_CMD`, `OC1INCR_MON`, `OC1START_CMD`, `OC1START_MON`, `OC2AXIS_CMD`, `OC2AXIS_STATUS`, `OC2INCR_CMD`, `OC2INCR_MON`, `OC2START_CMD`, `OC2START_MON`, `OCMESSAGE_MON` | [Controller PVs](controller-pvs.md) |
| [OE](galil-command-reference.md#pid--servo-tuning) | `_OFFONERR_CMD`, `_OFFONERR_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [PA](galil-command-reference.md#motion-commands) | `_JAHV_SP`, `_JAHV_MON` | [Motor Axis PVs](motor-axis-pvs.md) |
| [QU](galil-command-reference.md#array-commands) | `UPLOAD_CMD`, `UPLOAD_STATUS`, `UARRAY$(ADDR)_MON`, `UARRAYNAME$(ADDR)_SP` | [Controller PVs](controller-pvs.md), [User-Defined Records](user-defined-records.md) |
| [SB](galil-command-reference.md#io-commands) | `_AUTOBRAKE_CMD`, `_BRAKE_STATUS`, `_CMD` (Digital Out) | [Motor Axis PVs](motor-axis-pvs.md), [Digital I/O PVs](digital-io-pvs.md) |
| [SH](galil-command-reference.md#motor-enabledisable) | `_ON_CMD`, `_AUTOONOFF_CMD` | [Motor Axis PVs](motor-axis-pvs.md) |
| [SI](galil-command-reference.md#absolute-encoders) | `_SSIINPUT_CMD`, `_SSIINPUT_STATUS`, `_SSITOTBITS_SP`, `_SSITOTBITS_MON`, `_SSISINGLETBITS_SP`, `_SSISINGLETBITS_MON`, `_SSIERRBITS_SP`, `_SSIERRBITS_MON`, `_SSITIME_SP`, `_SSITIME_MON`, `_SSIDATA_CMD`, `_SSIDATA_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [SS](galil-command-reference.md#absolute-encoders) | `_BISSINPUT_CMD`, `_BISSINPUT_STATUS`, `_BISSDATA1_SP`, `_BISSDATA1_MON`, `_BISSDATA2_SP`, `_BISSDATA2_MON`, `_BISSZP_SP`, `_BISSZP_MON`, `_BISSCD_SP`, `_BISSCD_MON` | [Motor Axis PVs](motor-axis-pvs.md) |
| [ST](galil-command-reference.md#motion-commands) | `DEFER_MODE_CMD`, `DEFER_MODE_STATUS`, `_STOP_CMD` | [Controller PVs](controller-pvs.md), [Coordinate System PVs](coordinate-system-pvs.md) |
| [SY](galil-command-reference.md#absolute-encoders) | `_BISSLEVEL_CMD`, `_BISSLEVEL_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
| [TA](galil-command-reference.md#status--query) | `_HALLERROR_STATUS`, `_OVERCURRENT_STATUS`, `_UNDERVOLTAGE_STATUS`, `_OVERVOLTAGE_STATUS`, `_OVERTEMPERATURE_STATUS`, `_ELO_STATUS` | [Motor Axis PVs](motor-axis-pvs.md), [Amplifier Status PVs](amplifier-status-pvs.md) |
| [YA](galil-command-reference.md#motor-configuration) | `_MICROSTEP_CMD`, `_MICROSTEP_STATUS` | [Motor Axis PVs](motor-axis-pvs.md) |
