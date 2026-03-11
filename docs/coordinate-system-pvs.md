# Coordinate System PVs

The Galil supports 2 coordinate systems (S and T) for coordinated multi-axis motion. CS axes I through P are virtual axes that map to real axes A through H through kinematic transforms.

## Coordinate System Records

Template: `galil_coordinate_system.template`

PV pattern: `$(P)$(R)_` where `R` is the coordinate system name (e.g., `S` or `T`). ADDR is 0 for S, 1 for T.

| PV Suffix | Record Type | Dir | asyn drvInfo | Description |
|-----------|-------------|-----|--------------|-------------|
| `_NAME_MON` | bi | R | - | Coordinate system name: S or T |
| `_MOTORS_MON` | stringin | R | COORDINATE_SYSTEM_MOTORS | List of motors assigned to this coordinate system |
| `_MOVN_STATUS` | bi | R | COORDINATE_SYSTEM_MOVING | Moving status: Stopped/Moving |
| `_SEGMENTS_MON` | ai | R | COORDINATE_SYSTEM_SEGMENTS | Number of segments processed |
| `_STOP_CMD` | bo | W | COORDINATE_SYSTEM_MOTORS_STOP | Stop all motors in coordinate system. Galil ST (Stop) |
| `_GO_CMD` | bo | W | COORDINATE_SYSTEM_MOTORS_GO | Begin all motors in coordinate system. Galil BG (Begin) |

## CS Motor Extras

Template: `galil_csmotor_extras.template`

PV pattern: `$(P)$(M)_` where `M` is the CS motor (e.g., `I`). ADDR is 8-15.

| PV Suffix | Record Type | Dir | asyn drvInfo | Description |
|-----------|-------------|-----|--------------|-------------|
| `_HOMING_STATUS` | bi | R | MOTOR_HOMING | CS axis homing status |
| `_HOMING_MONITOR` | calcout | R | - | Combined homing status from HOMR, HOMF, and HOMING_STATUS |
| `_SP_MON` | ai | R | CSMOTOR_SETPOINT | CS axis setpoint monitor. The demanded position for this CS axis |

## Forward Transform

Template: `galil_forward_transform.template`

PV pattern: `$(P)$(M)_`

| PV Suffix | Record Type | Dir | asyn drvInfo | Description |
|-----------|-------------|-----|--------------|-------------|
| `_FTRANSFORM_SP` | waveform (CHAR[256]) | W | CSMOTOR_FORWARD_TRANSFORM | Forward kinematic equation string. Maps real axis positions to CS axis position |

## Reverse Transforms

Template: `galil_reverse_transforms.template`

PV pattern: `$(P)$(M)_`. One reverse transform per real axis (A through H).

| PV Suffix | Record Type | Dir | asyn drvInfo | Description |
|-----------|-------------|-----|--------------|-------------|
| `_RTRANSFORMA_SP` | waveform (CHAR[256]) | W | CSMOTOR_REVERSEA_TRANSFORM | Reverse kinematic for axis A |
| `_RTRANSFORMB_SP` | waveform (CHAR[256]) | W | CSMOTOR_REVERSEB_TRANSFORM | Reverse kinematic for axis B |
| `_RTRANSFORMC_SP` | waveform (CHAR[256]) | W | CSMOTOR_REVERSEC_TRANSFORM | Reverse kinematic for axis C |
| `_RTRANSFORMD_SP` | waveform (CHAR[256]) | W | CSMOTOR_REVERSED_TRANSFORM | Reverse kinematic for axis D |
| `_RTRANSFORME_SP` | waveform (CHAR[256]) | W | CSMOTOR_REVERSEE_TRANSFORM | Reverse kinematic for axis E |
| `_RTRANSFORMF_SP` | waveform (CHAR[256]) | W | CSMOTOR_REVERSEF_TRANSFORM | Reverse kinematic for axis F |
| `_RTRANSFORMG_SP` | waveform (CHAR[256]) | W | CSMOTOR_REVERSEG_TRANSFORM | Reverse kinematic for axis G |
| `_RTRANSFORMH_SP` | waveform (CHAR[256]) | W | CSMOTOR_REVERSEH_TRANSFORM | Reverse kinematic for axis H |

## How Kinematic Transforms Work

**Forward transforms** compute CS axis positions from real axis positions. For example, a forward transform for CS axis I might be defined as `(A+B)/2`, meaning the virtual axis position is the average of real axes A and B.

**Reverse transforms** compute demanded real axis positions from CS axis positions. Continuing the example, the reverse transforms would express A and B in terms of I (and possibly other CS axes).

These are mathematical expressions stored as strings and evaluated by the driver using EPICS sCalcout-style syntax.
