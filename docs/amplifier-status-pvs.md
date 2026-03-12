# Amplifier Status PVs

For Galil controllers with internal amplifiers (AMP/SDM options), the IOC monitors amplifier health. Status is derived from the Galil [`TA`](galil-command-reference.md#ta) (Tell Amplifier error status) command bits, parsed from the data record.

## Quad Amplifier Status

Template: `galil_quadAmpStatus.template`

PV pattern: `$(P)$(AMP)_` where `AMP` is the amplifier bank name. ADDR maps to the amplifier bank.

| PV Suffix | Record Type | asyn drvInfo | Galil Cmd | Description |
|-----------|-------------|--------------|-----------|-------------|
| `_OVERCURRENT_STATUS` | bi | AMP_OVERCURRENT_STATUS | [TA](galil-command-reference.md#ta) bit, data record | Overcurrent fault (MAJOR alarm) |
| `_UNDERVOLTAGE_STATUS` | bi | AMP_UNDERVOLTAGE_STATUS | [TA](galil-command-reference.md#ta) bit, data record | Undervoltage fault (MAJOR alarm) |
| `_OVERVOLTAGE_STATUS` | bi | AMP_OVERVOLTAGE_STATUS | [TA](galil-command-reference.md#ta) bit, data record | Overvoltage fault (MAJOR alarm) |
| `_OVERTEMPERATURE_STATUS` | bi | AMP_OVERTEMPERATURE_STATUS | [TA](galil-command-reference.md#ta) bit, data record | Overtemperature fault (MAJOR alarm) |
| `_ELO_STATUS` | bi | AMP_ELO_STATUS | [TA](galil-command-reference.md#ta) bit, data record | Electronic lockout active (MAJOR alarm) |

All status PVs use I/O Intr scanning and transition to MAJOR alarm severity on fault. To clear latched faults, use the controller-level `CLEARAMPFAULTS_CMD` PV, which issues the Galil [`AZ`](galil-command-reference.md#az) (Clear Latched Amplifier Errors) command.
