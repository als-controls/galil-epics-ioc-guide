# Profile/Trajectory PVs

The IOC supports trajectory scanning and profile moves using the asynMotor profile move interface. Both Linear interpolation and PVT (Position-Velocity-Time) modes are supported. The driver uses Galil [`LI`](galil-command-reference.md#li)/[`LE`](galil-command-reference.md#le) (Linear Interpolation) or [`PV`](galil-command-reference.md#pv)/[`BT`](galil-command-reference.md#bt) (PVT Data/Begin PVT) commands depending on the selected mode.

## Profile Controller

Template: `galil_profileMoveController.template`

PV pattern: `$(P)$(R)` where `R` is the profile prefix.

| PV Suffix | Record Type | asyn drvInfo | Description |
|-----------|-------------|--------------|-------------|
| `TrajectoryFile` | stringout | GALIL_PROFILE_FILE | Trajectory file name (default "TrajectoryScan.trj") |
| `PROFILETYPE_CMD` | bo | GALIL_PROFILE_TYPE | Profile type: Linear or PVT |
| `PROFILETYPE_STATUS` | bi | GALIL_PROFILE_TYPE | Profile type readback |

Additional profile PVs are inherited from the standard asynMotor profile move base class, including Build, Execute, Abort, and ReadBack.

## Profile Axis

Template: `galil_profileMoveAxis.template`

PV pattern: `$(P)$(R)M$(M)`. One set of records is created per axis.

| PV Suffix | Record Type | asyn drvInfo | Description |
|-----------|-------------|--------------|-------------|
| `CalculatedPositions` | waveform (DOUBLE) | GALIL_PROFILE_CALCULATED | Calculated position array for this axis |
| `MinPosition` | ai | GALIL_PROFILE_MIN_POSITION | Minimum position in trajectory |
| `MaxPosition` | ai | GALIL_PROFILE_MAX_POSITION | Maximum position in trajectory |
| `MaxVelocity` | ai | GALIL_PROFILE_MAX_VELOCITY | Maximum velocity in trajectory |
| `MaxAcceleration` | ai | GALIL_PROFILE_MAX_ACCELERATION | Maximum acceleration in trajectory |
| `MoveMode` | bo | GALIL_PROFILE_MOVE_MODE | Move mode: Absolute or Relative |

## Startup Configuration

Profile moves require the following call in the IOC startup script:

```
GalilCreateProfile(portName, maxPoints)
```

where `portName` is the asyn port name of the controller and `maxPoints` is the maximum number of trajectory points.
