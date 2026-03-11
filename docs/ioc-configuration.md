# IOC Configuration

This section covers the Galil-specific IOC shell commands, environment variables, database loading conventions, and autosave configuration needed to set up a working Galil EPICS IOC.

## IOC Shell Commands

### GalilCreateController

```
GalilCreateController(portName, address, updatePeriod)
```

Creates a connection to a Galil controller and registers an asyn port.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name (e.g. `"DMC01"`, `"RIO01"`) |
| `address` | Controller IP address or serial port (e.g. `"192.168.0.67"`) |
| `updatePeriod` | Data record update period in ms (valid range: 2--200). A positive value tries asynchronous UDP first and falls back to TCP. A negative value forces synchronous TCP only. For ethernet connections, 50 ms or less is recommended; 8 ms is typical. |

### GalilCreateAxis

```
GalilCreateAxis(portName, axis, motorInterlocks, switchType)
```

Creates a motor axis on a previously created controller.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name matching a prior `GalilCreateController` call |
| `axis` | Axis letter, `A` through `H` (up to 8 axes per controller) |
| `motorInterlocks` | Comma-separated digital port numbers (1--8) that interlock this motor (e.g. `"1,2,4"`). Only the first 8 bits are supported. Use `""` for no interlocks. |
| `switchType` | Interlock switch type. `0` = interlock is active when the opto is active. Any other value = interlock is active when the opto is inactive. |

### GalilCreateCSAxes

```
GalilCreateCSAxes(portName)
```

Creates all coordinate system axes (I--P) on the specified controller. This must be called **after** all `GalilCreateAxis` calls for that controller.

### GalilStartController

```
GalilStartController(portName, codeFile, burnProgram, threadMask)
```

Downloads DMC code to the controller and starts execution.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name |
| `codeFile` | DMC code file(s) to deliver. Use `""` to use auto-generated code (recommended for most setups). For template-based code, specify header, body, and footer files in the format `"headerfile;bodyfile1!bodyfile2!bodyfileN;footerfile"`. |
| `burnProgram` | `0` = transfer code if it differs from what is on the controller, do not burn to EEPROM, execute thread 0. `1` = transfer code if it differs, burn to EEPROM, execute thread 0. |
| `threadMask` | Specifies which threads to verify are running after start. Bit 0 corresponds to thread 0, bit 1 to thread 1, and so on. A negative value disables the check. `0` checks threads 0 through N, where N is the number of `GalilCreateAxis` calls -- this is the correct setting when using auto-generated code. |

### GalilCreateProfile

```
GalilCreateProfile(portName, maxPoints)
```

Enables profile (trajectory) motion support on the controller.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name |
| `maxPoints` | Maximum number of points in a trajectory (e.g. `2000`) |

### GalilAddCode

```
GalilAddCode(portName, section, codeFile)
```

Inserts custom DMC code into the auto-generated code at a specified section.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name |
| `section` | Target section: `0` = card code, `1` = thread code, `2` = limits code, `3` = digital code |
| `codeFile` | Path to the custom DMC code file |

### GalilReplaceHomeCode

```
GalilReplaceHomeCode(portName, axis, codeFile)
```

Replaces the auto-generated homing routine for a single axis with custom DMC code from a file.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name |
| `axis` | Axis letter (`A`--`H`) |
| `codeFile` | Path to the custom homing code file |

## Environment Variables

The following environment variables are used in Galil IOC startup scripts and database substitution files.

| Variable | Description | Example |
|----------|-------------|---------|
| `PORT` | Asyn port name | `DMC01` |
| `ADDRESS` | Controller IP address or serial port | `192.168.0.67` |
| `UPDPERIOD` | Data record update period in ms | `8` |
| `P` | PV prefix, typically derived from `PORT` | `DMC01:` |
| `GALIL_DEBUG_FILE` | When set, all commands sent to the Galil controller are logged to this file | `galil_debug.txt` |

## Database Loading Order

A typical DMC startup script (e.g. `DMC01Configure.cmd`) loads databases in the following order:

1. **Motor records** -- real axes and coordinate system axes, loaded via substitution files.
2. **DMC controller features** -- `galil_dmc_ctrl` template (controller-wide status and settings).
3. **Amplifier status** -- `galil_quadAmpStatus` template (per-amplifier diagnostics).
4. **Motor extras** -- motor type, encoder type, and other per-axis settings beyond what the motor record provides.
5. **CS motor extras** -- additional settings for coordinate system axes.
6. **Kinematics** -- forward and reverse kinematic transform definitions.
7. **Coordinate system features** -- coordinate system status and control records.
8. **Digital I/O** -- digital input and output port records.
9. **Analog I/O** -- analog input and output port records.
10. **User-defined records** -- any site-specific records.
11. **User arrays** -- Galil user array records.
12. **Profile move support** -- profile move controller and per-axis trajectory records.

## Autosave Configuration

Three restore files are typically configured to preserve state across IOC restarts.

**Pass 0** (restored before record initialization):

- `Settings.sav` -- encoder ratios, step sizes, and other motor settings. These must be restored before position records initialize so that raw counts are converted correctly.
- `Positions.sav` -- motor positions. Restoring positions at pass 0 prevents motors from moving to zero on restart.

**Pass 1** (restored after record initialization):

- `Kinematics.sav` -- kinematic equation character arrays (forward and reverse transform expressions). These are restored at pass 1 because the waveform records that hold them must be initialized first.

## DMC vs RIO

Galil produces two categories of controller relevant to IOC configuration: DMC and RIO. The choice determines which templates and shell commands are appropriate.

**DMC (Digital Motion Controller)** provides full motion control capability: motor axes, coordinate system axes, digital and analog I/O, profile moves, and output compare. The IOC uses `galil_dmc_ctrl.template` for controller-level records and calls `GalilCreateAxis`, `GalilCreateCSAxes`, and the full set of shell commands documented above.

**RIO (Remote I/O)** provides digital and analog I/O only, with no motor axis support. The IOC uses `galil_rio_ctrl.template`, which omits limit/home switch type configuration, deferred move support, coordinate systems, and output compare. A RIO startup script calls `GalilCreateController` and `GalilStartController` but does not call `GalilCreateAxis`, `GalilCreateCSAxes`, or `GalilCreateProfile`.
