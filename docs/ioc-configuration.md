# IOC Configuration

This section covers everything needed to build, configure, and run a Galil EPICS IOC: build requirements, shell commands, database substitution files, autosave setup, and custom code.

## Building

### Compiler Requirements

The Galil EPICS driver requires a C++11-compliant compiler because it uses the `unordered_map` container. On Linux, gcc 4.8.1 or later is recommended. Older Linux distributions may require the `devtoolset2` toolchain or newer. On Windows, Visual Studio 2010 or later is required.

### EPICS Base

EPICS base version 3.14.12.2 or newer is required.

### Required Support Modules

| Module | Minimum Version |
|--------|-----------------|
| ASYN | 4-26 |
| Autosave | 5-5 |
| SNCSEQ | 2-1-8 |
| SSCAN | 2-8-1 |
| CALC | 3-4-2 |
| Busy | 1-6 |
| Motor | 6-8-1 |
| IPAC | 2-11 |

### Release Configuration

The driver has both a `config` directory and a standard `configure` directory. The file `config/GALILRELEASE` serves as both a standard release file and a master release file. The `configure/RELEASE` file simply includes `GALILRELEASE`. The `config` directory supports building both the driver and the included example IOC using the SynApps build approach.

To define the paths to all required support modules, either:

1. Populate `config/GALILRELEASE` as a normal release file, or
2. Populate `configure/RELEASE` as a normal release file and delete the line that includes `$(TOP)/config/GALILRELEASE`.

## Creating an IOC

### Create the Application

Create an IOC application using the EPICS `makeBaseApp.pl` script:

```
mkdir <IOCName>
cd <IOCName>
makeBaseApp.pl -t ioc <IOCName>
makeBaseApp.pl -i -t ioc <IOCName>
```

### Adding Galil Support

Add the following lines to `<IOCApp>/src/Makefile`:

```makefile
<IOCName>_DBD += GalilSupport.dbd
<IOCName>_LIBS += GalilSupport
```

## IOC Shell Commands

### GalilCreateController

```
GalilCreateController(portName, address, updatePeriod)
```

Creates a connection to a Galil controller and registers an asyn port. This must be called before all other Galil driver commands for a given controller.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name (e.g. `"Galil1"`, `"RIO01"`) |
| `address` | Controller IP address or serial port (e.g. `"192.168.0.67"`, `"COM1"`) |
| `updatePeriod` | Data record update period in ms (valid range: 2--200). A positive value tries asynchronous UDP first and falls back to TCP. A negative value forces synchronous TCP only. For ethernet connections, 50 ms or less is recommended; 8 ms is typical. |

Example -- two controllers:

```
GalilCreateController("Galil1", "192.168.0.67", 8)
GalilCreateController("Galil2", "192.168.0.68", 8)
```

### GalilCreateAxis

```
GalilCreateAxis(portName, axis, limitsAsHome, motorInterlocks, switchType)
```

Creates a motor axis on a previously created controller. Must be called after `GalilCreateController` and before `GalilStartController`.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name matching a prior `GalilCreateController` call |
| `axis` | Axis letter, `A` through `H` (up to 8 axes per controller) |
| `limitsAsHome` | Use limit switches as home switches. `0` = off (default), `1` = on. |
| `motorInterlocks` | Comma-separated digital port numbers (1--8) that interlock this motor (e.g. `"1,2,4"`). Only the first 8 bits are supported. Use `""` for no interlocks. |
| `switchType` | Interlock switch type. `0` = interlock is active when the opto is active. Any other value = interlock is active when the opto is inactive. |

Example -- two axes on each of two controllers:

```
GalilCreateAxis("Galil1", "A", 1, "", 1)
GalilCreateAxis("Galil1", "B", 1, "", 1)

GalilCreateAxis("Galil2", "A", 1, "", 1)
GalilCreateAxis("Galil2", "B", 1, "", 1)
```

### GalilCreateCSAxes

```
GalilCreateCSAxes(portName)
```

Creates all coordinate system axes (I--P) on the specified controller. This must be called **after** all `GalilCreateAxis` calls for that controller, and before `GalilStartController`.

Example:

```
GalilCreateCSAxes("Galil1")
GalilCreateCSAxes("Galil2")
```

### GalilStartController

```
GalilStartController(portName, codeFile, burnProgram, threadMask)
```

Downloads DMC code to the controller and starts execution. Must be called after all other Galil driver commands for the controller, but before `iocInit()`.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name |
| `codeFile` | DMC code file(s) to deliver. Use `""` to use auto-generated code (recommended for most setups). For template-based code, specify header, body, and footer files in the format `"headerfile;bodyfile1!bodyfile2!bodyfileN;footerfile"`. |
| `burnProgram` | `0` = transfer code if it differs from what is on the controller, do not burn to EEPROM, execute thread 0. `1` = transfer code if it differs, burn to EEPROM, execute thread 0. |
| `threadMask` | Specifies which threads to verify are running after start. Bit 0 corresponds to thread 0, bit 1 to thread 1, and so on. A negative value disables the check. `0` checks threads 0 through N, where N is the number of `GalilCreateAxis` calls -- this is the correct setting when using auto-generated code. |

Example:

```
GalilStartController("Galil1", "", 1, 0)
GalilStartController("Galil2", "", 1, 0)
```

The driver writes a copy of the code delivered to the controller to the `iocBoot` directory with extension `.gmc`.

### GalilCreateProfile

```
GalilCreateProfile(portName, maxPoints)
```

Enables profile (trajectory) motion support on the controller. Must be called after all other Galil driver commands except `GalilStartController`.

| Parameter | Description |
|-----------|-------------|
| `portName` | Asyn port name |
| `maxPoints` | Maximum number of points in a trajectory (e.g. `2000`) |

Example:

```
GalilCreateProfile("Galil1", 2000)
GalilCreateProfile("Galil2", 2000)
```

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
| `PORT` | Asyn port name | `Galil1` |
| `ADDRESS` | Controller IP address or serial port | `192.168.0.67` |
| `UPDPERIOD` | Data record update period in ms | `8` |
| `P` | PV prefix, typically derived from `PORT` | `DMC01:` |
| `GALIL_DEBUG_FILE` | When set, all commands sent to the Galil controller are logged to this file | `galil_debug.txt` |

## Database Substitution Files

Each driver feature has a corresponding substitution file that loads the EPICS database records for that feature. Below are representative examples showing the substitution file format and macros for each feature category.

### General Principles

Configuring a feature typically requires three steps:

1. Configure autosave request files for the feature.
2. Configure the database substitution file for the feature.
3. Add the appropriate shell commands to the IOC start script.

It is recommended that only the controller prefix (`P`) be changed between controllers. To identify individual motors or ports, use the record `DESC` field rather than changing record names. The description field is shown on all provided display screens and is saved/restored by autosave.

Hardware configuration (motor type, encoder type, etc.) is done at runtime through the provided display screens and saved by autosave -- not through substitution file macros.

### Controller

The controller database is loaded using `galil_ctrl_extras.substitutions`:

```
# P    - PV prefix
# PORT - Asyn port name
# SCAN - Scan period for monitor records. "Passive" recommended (default).
# DEFAULT_LIMITTYPE - 0 Normally open, 1 Normally closed
# DEFAULT_HOMETYPE - 0 Normally open, 1 Normally closed
# PREC - Precision

file "$(GALIL)/GalilSup/Db/galil_ctrl_extras.template"
{
pattern
{ P,       PORT,      SCAN,      DEFAULT_HOMETYPE, DEFAULT_LIMITTYPE, PREC}
{ "DMC01", "Galil1",  "Passive", 1,                1,                 5 }
}
```

For RIO installations, only the start script section of the controller configuration is needed -- the autosave and database sections can be skipped.

### Motor Records

The motor database is loaded using `galil_motors.substitutions`. At minimum, the `galil_motor.template` file must be used. Loading motor records with the templates that ship with the EPICS motor record (instead of the Galil-specific template) is not supported.

Each motor (real axes A--H and CS axes I--P) requires a single line entry. Motor record addresses are numbered from 0: real axes are 0--7 and CS axes are 8--15.

### Motor Extras

Motor extras provide per-axis features not covered by the EPICS motor record (motor type, encoder type, home type, etc.). Motor extras are mandatory for real motors and are not used for CS motors.

The motor extras database is loaded using `galil_motor_extras.substitutions`:

```
# P       - Controller prefix
# M       - Motor name
# PORT    - Asyn port of controller
# ADDR    - Axis number 0-7
# PREC    - Precision of analog records
# SCAN    - Scan period for monitor records. "Passive" recommended.
# MTRTYPE - Motor type:
#            0 - Servo
#            1 - Reverse servo
#            2 - High active stepper
#            3 - Low active stepper
#            4 - Reverse high active stepper
#            5 - Reverse low active stepper
# MTRON   - Motor off 0, motor on 1
# EGU     - Engineering units

file "$(GALIL)/GalilSup/Db/galil_motor_extras.template"
{
pattern
{ P,       M,   PORT,     ADDR, PREC, SCAN,      MTRTYPE, MTRON, EGU }
{ "DMC01", "A", "Galil1", 0,    3,    "Passive", "0",     "0",   "mm" }
{ "DMC01", "B", "Galil1", 1,    3,    "Passive", "0",     "0",   "mm" }
}
```

### CS Motors

CS motors use the EPICS motor record, so the autosave and database setup is identical to that for real motors (see Motor Records above). However, CS motor positions are not managed by autosave -- they are calculated from real motor positions via the forward kinematic transform.

### Coordinate Systems

Galil controllers provide two hardware coordinate systems, S and T. Coordinate systems are used when coordinating the motion of two or more motors in linear interpolation mode. They are also used for "sync start and stop" deferred moves and "linear mode" profile motion.

In PVT mode (Accelera series and above), more than two motors can be coordinated without using a coordinate system. For this reason, configuring coordinate systems is not mandatory in all cases, but is recommended for completeness.

Autosave is not used for coordinate systems.

The coordinate system database is loaded using `galil_coordinate_systems.substitutions`:

```
# P    - PV prefix
# R    - Record Name (S or T)
# PORT - Asyn port name
# ADDR - Hardware port to read (0 for S, 1 for T)
# SCAN - Scan period for monitor records

file "$(GALIL)/GalilSup/Db/galil_coordinate_system.template"
{
pattern { P,       R,   PORT,     ADDR, SCAN          }
        { "DMC01", "S", "Galil1", "0",  ".1 second" }
        { "DMC01", "T", "Galil1", "1",  ".1 second" }
}
```

### Kinematics

Kinematics define the relationship between real motors and CS motors. For each CS motor there is one forward transform (readback) and eight reverse transforms (setpoints, one per real motor A--H). Kinematic variables (Q--Z) have controller-wide scope and can be used in transform expressions. All equations and variables are runtime-adjustable via the EPICS database.

The kinematics database is loaded using `galil_csmotor_kinematics.substitutions`, which has three sections: forward transforms, reverse transforms, and kinematic variables.

Forward transforms (one per CS motor):

```
file "$(GALIL)/GalilSup/Db/galil_forward_transform.template"
{
  pattern { P,       M,   PORT,     ADDR }
  # CS motor I (address 8)
          { "DMC01", "I", "Galil1", "8"  }
}
```

Reverse transforms (one per CS motor, maps back to real motors):

```
file "$(GALIL)/GalilSup/Db/galil_reverse_transforms.template"
{
  pattern { P,       M,   PORT,     ADDR }
          { "DMC01", "I", "Galil1", "8"  }
}
```

Kinematic variables (10 per controller, Q--Z, addressed 0--9):

```
file "$(GALIL)/GalilSup/Db/galil_kinematic_variable.template"
{
  pattern { P,       R,   PORT,     ADDR, PREC }
          { "DMC01", "Q", "Galil1", "0",  "5"  }
}
```

An example substitution file is provided at `GalilTestApp/Db/galil_csmotor_kinematics.substitutions`.

### Analog I/O

The analog I/O database is loaded using `galil_analog_ports.substitutions`. The file has separate sections for input and output ports.

DMC ports are numbered 1--8 on the controller hardware but 0--7 at the database layer for the GUI.

Input ports:

```
# P    - PV prefix
# R    - Record Name
# PORT - Asyn port name
# ADDR - Hardware port to read
# SCAN - Scan period
# PREC - Precision

file "$(GALIL)/GalilSup/Db/galil_analog_in.template"
{
  pattern { P,       R,           PORT,     ADDR, SCAN,       PREC }
          { "DMC01", "GalilAi0",  "Galil1", "1",  "I/O Intr", "3" }
          { "DMC01", "GalilAi1",  "Galil1", "2",  "I/O Intr", "3" }
}
```

Output ports:

```
# P    - PV prefix
# R    - Record Name
# PORT - Asyn port name
# ADDR - Hardware port
# PREC - Precision
# LOPR - Low operating range
# HOPR - High operating range

file "$(GALIL)/GalilSup/Db/galil_analog_out.template"
{
  pattern { P,       R,           PORT,     ADDR, PREC, LOPR,  HOPR }
          { "DMC01", "GalilAo0",  "Galil1", "1",  "3",  "-10", "10" }
          { "DMC01", "GalilAo1",  "Galil1", "2",  "3",  "-10", "10" }
}
```

### Digital I/O

On DMC controllers, digital inputs are organized in bytes and digital outputs are organized in 16-bit words. On RIO units, both inputs and outputs are organized in bytes. However, at the database layer the RIO digital outputs are named in words (to be consistent with DMC units) so the same GUI can be used.

The digital I/O database is loaded using `galil_digital_ports.substitutions`. The file has separate sections for input and output bit records.

Input bits:

```
# P    - PV prefix
# R    - Record Name
# PORT - Asyn port name
# BYTE - Hardware byte to read
# MASK - Mask for this bit
# ZNAM/ONAM - State names
# ZSV/OSV   - Alarm severities

file "$(GALIL)/GalilSup/Db/galil_digital_in_bit.template"
{
  pattern {P,       R,           PORT,     BYTE, MASK,      ZNAM,  ONAM, ZSV,        OSV        }
          {DMC01,   Galil0Bi0,   Galil,    0,    0x000001,  "Off", "On", "NO_ALARM", "NO_ALARM" }
          {DMC01,   Galil0Bi1,   Galil,    0,    0x000002,  "Off", "On", "NO_ALARM", "NO_ALARM" }
}
```

Output bits:

```
# P    - PV prefix
# R    - Record Name
# PORT - Asyn port name
# WORD - Hardware word to read
# MASK - Mask for this bit

file "$(GALIL)/GalilSup/Db/galil_digital_out_bit.template"
{
  pattern {P,       R,           PORT,     WORD, MASK,      ZNAM,  ONAM, ZSV,        OSV        }
          {DMC01,   Galil0Bo0,   Galil,    0,    0x000001,  "Off", "On", "NO_ALARM", "NO_ALARM" }
          {DMC01,   Galil0Bo1,   Galil,    0,    0x000002,  "Off", "On", "NO_ALARM", "NO_ALARM" }
}
```

### Profile Motion

Profile motion involves one or more motors following a user-defined position profile, executed using linear interpolation mode or PVT mode (Accelera series and above). Some profile features relate to the controller and others to the individual axes.

Autosave is not used for profile motion settings.

Controller-level profile records are loaded using `galil_profileMoveController.substitutions`:

```
# Profile move controller (from motor module)
file "$(MOTOR)/db/profileMoveController.template"
{
pattern
{P,       R,      PORT,   NAXES, NPOINTS, NPULSES, TIMEOUT}
{DMC01:,  Prof1:, Galil,  8,     1441,    1441,    1}
}

# Galil-specific profile move controller features
file "$(GALIL)/GalilSup/Db/galil_profileMoveController.template"
{
pattern
{P,       R,      PORT,   TIMEOUT}
{DMC01:,  Prof1:, Galil,  1}
}
```

Per-axis profile records are loaded using `galil_profileMoveAxes.substitutions`. Refer to `GalilTestApp/Db/galil_profileMoveAxis.substitutions` for an example.

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

Autosave preserves settings and positions across IOC restarts. Configuring autosave typically requires three things: request files, a configuration script, and monitor set creation in the IOC start script.

### Request Files

Two autosave request files should be created in the `iocBoot` directory for each IOC:

- `IOCName_settings.req` -- saves all settings for connected controllers and their motors.
- `IOCName_positions.req` -- saves motor positions.

### Configuration Script

An autosave command script (e.g. `autosave.cmd`) should be added to the `iocBoot` directory and sourced from the IOC start script. It must contain the request file search paths so autosave can find the provided `.req` files:

```
set_requestfile_path("${GALIL}/GalilSup/Db", "")
set_requestfile_path("${MOTOR}/motorApp/Db", "")
set_requestfile_path("${TOP}/iocBoot/${IOC}", "")
```

It must also configure the restore operations. Positions and settings are restored at pass 0 (before record initialization) so that raw counts are converted correctly and motors do not move to zero on restart:

```
# Restore positions at pass 0 so motors don't move
set_pass0_restoreFile("IOCName_positions.sav")
# Restore settings at pass 0
set_pass0_restoreFile("IOCName_settings.sav")
```

Optionally, specify where autosave stores its backup files:

```
set_savefile_path("/autosave", "")
```

Kinematic equations (stored in waveform records) must be restored at pass 1, after record initialization:

- `Kinematics.sav` -- kinematic equation character arrays (forward and reverse transform expressions).

### Monitor Sets

In the IOC start script, after `iocInit()`, create monitor sets for each request file:

```
# Save motor positions every 5 seconds
create_monitor_set("IOCName_positions.req", 5, "P1=DMC01:, P2=DMC02:")
# Save motor settings every 30 seconds
create_monitor_set("IOCName_settings.req", 30, "P1=DMC01:, P2=DMC02:")
```

### Per-Feature Request Files

The driver provides ready-made `.req` files for each feature. Add lines to `IOCName_settings.req` for each feature in use:

**Controller** (one per controller):

```
file "galil_ctrl_extras.req" P=$(P)
```

**Motor records** (one per motor, real and CS):

```
file "motor_settings.req" P=$(P), M=A
```

**Motor positions** (add to `IOCName_positions.req`, one per real motor):

```
$(P)A.DVAL
```

CS motor positions are not saved -- they are calculated from real motor positions.

**Motor extras** (one per real motor):

```
file "galil_motor_extras.req" P=$(P), M=A
```

**Analog inputs** (one per channel):

```
file "galil_analog_in.req" P=$(P), R=GalilAi0
```

**Analog outputs** (one per channel):

```
file "galil_analog_out.req" P=$(P), R=GalilAo0
```

**Digital inputs** (one per bit):

```
file "galil_digital_in.req" P=$(P), R=Galil0Bi0
```

**Digital outputs** (one per bit):

```
file "galil_digital_out.req" P=$(P), R=Galil0Bo0
```

**Kinematics -- forward transforms** (one per CS motor):

```
file "galil_forward_transform.req" P=$(P), M=I
```

**Kinematics -- reverse transforms** (one per CS motor):

```
file "galil_reverse_transforms.req" P=$(P), M=I
```

**Kinematics -- variables** (one per variable, Q--Z):

```
file "galil_kinematic_variable.req" P=$(P), R=Q
```

## Custom Controller Code

The DMC code that runs on the controller defines homing behavior, limit switch activation behavior, and motor enable/interlock behavior for each motor. In most cases, the auto-generated code from the Galil EPICS driver is sufficient, and the `codeFile` parameter of `GalilStartController` should be an empty string `""`.

When custom code is needed, the recommended process is:

1. Run the IOC once with no code file specified in `GalilStartController` (use `""`).
2. Open the auto-generated code file that the driver dumps to the `iocBoot` directory (extension `.gmc`).
3. Add custom code in the correct thread, without changing the overall structure.
4. Save the file with a meaningful name.
5. Specify the custom code file in `GalilStartController`, or use `GalilAddCode` / `GalilReplaceHomeCode` to inject code into specific sections.

## Logging

Solicited command/response traffic between the IOC and controller can be logged to a file. To enable logging, add the following to the IOC start script:

```
epicsEnvSet("GALIL_DEBUG_FILE", "galil_debug.txt")
```

When using the provided example IOC, uncomment the corresponding line in `iocBoot/iocGalil/st.cmd`.

## User Display Configuration

MEDM and Qt (QEGUI) displays are provided with the driver. The Qt screens are recommended as they provide a more intuitive interface, a modern look and feel, and "user levels" to protect engineering settings from accidental changes.

### MEDM

- DMC controllers: launch with the `start_dmc_screen.sh` script.
- RIO controllers: launch with the `start_rio_screen.sh` script.

The screen start scripts use `config/GALILRELEASE` to construct the `EPICS_DISPLAY_PATH` environment variable so that MEDM can find the necessary community screens and the Galil-specific screens. Depending on the release configuration approach chosen (see Building above), the scripts may need to reference `configure/RELEASE` instead.

### QEGUI

The QEGUI framework (a Qt-based EPICS display manager developed at the Australian Synchrotron) is available at [https://sourceforge.net/projects/epicsqt/](https://sourceforge.net/projects/epicsqt/).

- DMC controllers: launch with the `start_qedmc_screen.sh` script.
- RIO controllers: launch with the `start_qerio_screen.sh` script.

These scripts use `config/GALILRELEASE` to construct the `QE_UI_PATH` environment variable.

## DMC vs RIO

Galil produces two categories of controller relevant to IOC configuration: DMC and RIO. The choice determines which templates and shell commands are appropriate.

**DMC (Digital Motion Controller)** provides full motion control capability: motor axes, coordinate system axes, digital and analog I/O, profile moves, and output compare. The IOC uses `galil_dmc_ctrl.template` for controller-level records and calls `GalilCreateAxis`, `GalilCreateCSAxes`, and the full set of shell commands documented above.

**RIO (Remote I/O)** provides digital and analog I/O only, with no motor axis support. The IOC uses `galil_rio_ctrl.template`, which omits limit/home switch type configuration, deferred move support, coordinate systems, and output compare. A RIO startup script calls `GalilCreateController` and `GalilStartController` but does not call `GalilCreateAxis`, `GalilCreateCSAxes`, or `GalilCreateProfile`.

---

*The building requirements, substitution file examples, autosave configuration details, custom code workflow, and user display information in this section are adapted from the "Galil EPICS Driver Configuration Guide" (Rev 1.2, June 2016) by Mark Clift, Australian Synchrotron. Refer to the [original documentation](https://github.com/motorapp/Galil/tree/master/documentation) for the full document.*
