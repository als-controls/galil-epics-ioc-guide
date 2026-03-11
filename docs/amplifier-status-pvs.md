# Amplifier Status PVs

For Galil controllers with internal amplifiers (AMP/SDM options), the IOC monitors amplifier health. Status is derived from the Galil `TA` (Tell Amplifier error status) command bits, parsed from the data record.

## Quad Amplifier Status

Template: `galil_quadAmpStatus.template`

PV pattern: `$(P)$(AMP)_` where `AMP` is the amplifier bank name. ADDR maps to the amplifier bank.

| PV Suffix | Record Type | Dir | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|-----|--------------|-----------|-------------|
| `_OVERCURRENT_STATUS` | bi | R | AMP_OVERCURRENT_STATUS | TA bit, data record | Overcurrent fault (MAJOR alarm) |
| `_UNDERVOLTAGE_STATUS` | bi | R | AMP_UNDERVOLTAGE_STATUS | TA bit, data record | Undervoltage fault (MAJOR alarm) |
| `_OVERVOLTAGE_STATUS` | bi | R | AMP_OVERVOLTAGE_STATUS | TA bit, data record | Overvoltage fault (MAJOR alarm) |
| `_OVERTEMPERATURE_STATUS` | bi | R | AMP_OVERTEMPERATURE_STATUS | TA bit, data record | Overtemperature fault (MAJOR alarm) |
| `_ELO_STATUS` | bi | R | AMP_ELO_STATUS | TA bit, data record | Electronic lockout active (MAJOR alarm) |

All status PVs use I/O Intr scanning and transition to MAJOR alarm severity on fault. To clear latched faults, use the controller-level `CLEARAMPFAULTS_CMD` PV, which issues the Galil `AZ` (Clear Latched Amplifier Errors) command.
