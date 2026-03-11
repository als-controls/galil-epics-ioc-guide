# Galil EPICS IOC User's Guide

This guide documents the mapping between EPICS process variables and Galil hardware commands as implemented in [Mark Clift's Galil IOC](https://github.com/motorapp/Galil). It covers the asyn-based driver's PV interface, configuration parameters, and internal architecture for the DMC-41x3 controller series and RIO modules.

The intended audience is engineers and scientists who are already familiar with both EPICS (motor record, asyn, areaDetector conventions) and Galil motion controllers (DMC command language, data records, coordinate systems) and need to understand how the IOC bridges the two.

This guide covers the **V4.x driver line**.

---

## Where to Start

- **Setting up an IOC?** Start with [IOC Configuration](ioc-configuration.md) for startup commands and database loading, then [Controller PVs](controller-pvs.md) for controller-level settings.
- **Configuring motors?** Go straight to [Motor Axis PVs](motor-axis-pvs.md) — it covers the standard motor record mapping plus all Galil-specific extras (motor type, encoder setup, PID, homing, limits).
- **Want the big picture first?** Read [Architecture](architecture.md) for the communication flow, class structure, and how data records work.
- **Looking up a specific Galil command?** The [Galil Command Reference](galil-command-reference.md) appendix lists every 2-letter code the IOC uses.
- **Debugging polling or update rates?** See [Data Record & Polling](data-record-polling.md) for async UDP vs synchronous TCP and what comes from data records versus direct commands.

---

## Table of Contents

| Section | Description |
|---|---|
| [Architecture](architecture.md) | Communication flow, key C++ classes, polling model, and data record handling. |
| [IOC Configuration](ioc-configuration.md) | Startup script commands, `GalilCreateController` arguments, axis configuration, and `autosave` integration. |
| [Controller PVs](controller-pvs.md) | Controller-level PVs: connection status, error messages, code upload/download, and global settings. |
| [Motor Axis PVs](motor-axis-pvs.md) | Per-axis PVs for axes A–H: motor record fields, velocity, acceleration, PID tuning, limit configuration, homing, and encoder setup. |
| [Coordinate System PVs](coordinate-system-pvs.md) | Coordinate system PVs for systems S and T, and CS axes I–P: forward/reverse kinematics, coordinated motion, and kinematic variables. |
| [Analog I/O PVs](analog-io-pvs.md) | Analog input and output PVs: channel mapping, range selection, and scaling. |
| [Digital I/O PVs](digital-io-pvs.md) | Binary input/output PVs: bit-level access, port-level access, and interrupt-driven updates. |
| [Profile/Trajectory PVs](profile-trajectory-pvs.md) | Profile move and trajectory PVs: time arrays, position arrays, build/execute/abort, and linear interpolation mode. |
| [Amplifier Status PVs](amplifier-status-pvs.md) | Amplifier and drive status PVs: EtherCat drive status words, amplifier enable/fault, and brake control. |
| [User-Defined Records & Arrays](user-defined-records.md) | User-defined commands, custom records, and array support for arbitrary Galil command execution. |
| [Data Record & Polling](data-record-polling.md) | Data record structure, polling rates, async UDP versus synchronous TCP, and diagnostic counters. |
| [Galil Command Reference](galil-command-reference.md) | Quick reference of the Galil 2-letter commands used by the driver, organized by function. |

---

## Supported Hardware

### DMC-41x3 Series (Ethernet/PCI)

| Model | Axes |
|---|---|
| DMC-4113 | 1 |
| DMC-4123 | 2 |
| DMC-4133 | 3 |
| DMC-4143 | 4 |
| DMC-4153 | 5 |
| DMC-4163 | 6 |
| DMC-4173 | 7 |
| DMC-4183 | 8 |

### RIO Modules (Remote I/O)

| Model | Description |
|---|---|
| RIO-471x0 | RIO with analog/digital I/O (no motor axes) |
| RIO-472x0 | RIO with SER connectivity |
