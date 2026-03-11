# Appendix: Galil Command Reference

Quick reference of Galil 2-letter commands used by the IOC. Full documentation is available in the Galil Command Reference manual from Galil Motion Control.

Units throughout are **counts** (position), **counts/sec** (velocity), **counts/sec²** (acceleration), unless otherwise noted. Axis letters A–H are appended to commands (e.g., `SPA=10000` sets axis A speed).

## Motion Commands

| Code | Full Name | Description |
|------|-----------|-------------|
| BG | Begin | Start motion on one or more axes. `BGX` begins axis X; `BGS` begins coordinated motion on system S. The IOC issues BG after setting up position/speed. |
| ST | Stop | Decelerate to a stop. `STX` stops axis X; `STS` stops coordinate system S. Uses the DC deceleration rate. |
| PA | Position Absolute | Command an absolute move. `PAA=25000` moves axis A to position 25000. The motor record writes PA for absolute moves. |
| PR | Position Relative | Command a relative move. `PRA=1000` moves axis A 1000 counts from current position. Used by the motor record for relative moves. |
| JG | Jog | Command continuous motion at a velocity. `JGA=5000` jogs axis A at 5000 counts/sec. Used by the IOC for velocity-mode (JOG) moves. |
| SP | Speed | Set slew speed for PA/PR moves. `SPA=25000` sets axis A to 25000 counts/sec. Does not apply to JG moves (JG includes its own speed). |
| AC | Acceleration | Set acceleration for independent moves. `ACA=256000` sets 256000 counts/sec². Applies to PA, PR, and JG moves. |
| DC | Deceleration | Set deceleration for independent moves. Same units as AC. Also used as the stop rate for ST and limit-switch deceleration if SD is not set. |
| IT | Independent Time Constant | S-curve smoothing filter, range 0.0–1.0. `ITA=1.0` means no smoothing (instant accel); lower values smooth the velocity profile. Default 1.0. |
| HM | Home | Begin homing sequence using the home switch and/or index pulse. Behavior depends on CN switch polarity. The IOC implements several homing strategies via generated DMC code. |
| FI | Find Index | Jog until the encoder index pulse is detected, then stop and define position. Used as part of some homing routines. |
| FE | Find Edge | Jog until a home switch transition is detected, then stop. Used as part of some homing routines to locate the switch edge precisely. |
| BG S / BG T | Begin Coordinate | Start coordinated motion on system S or T. Issued after loading linear interpolation or PVT segments. |
| ST S / ST T | Stop Coordinate | Stop coordinated motion on the specified system. |

## Motor Configuration

| Code | Full Name | Description |
|------|-----------|-------------|
| MT | Motor Type | Set motor type per axis. Key values: `1.0`=servo, `−1.0`=reversed servo, `2.0`=stepper (active low), `−2.0`=stepper (active high), `2.5`=stepper with encoder, `−2.5`=reversed. Also values for servo II, brushless. |
| CE | Configure Encoder | Set encoder type. `0`=normal quadrature, `2`=pulse and direction, `4`=reversed quadrature, `6`=reversed pulse/dir. The IOC sets this based on the `_CE_Mon` PV. |
| BR | Brush Axis | Brushless motor configuration. `0`=brush motor (default), `1`=sinusoidal commutation (hall), `2`=external commutation. |
| AG | Amplifier Gain | Set amplifier gain. `0`=±5V range (default), `1`=0–5V, `2`=0–10V, `3`=±10V, `5`=±12V. Determines the DAC output scaling. |
| AU | Amplifier Current Loop | Configure current loop. First argument is axis, then up to 3 current loop parameters. Used for Galil integrated amplifiers. |
| KS | Step Motor Smoothing | Smoothing factor for stepper motors, range 0.25–64. `KSA=2` sets axis A smoothing to 2. Reduces vibration but limits top speed. Default 2. |
| YA | Step Drive Resolution | Set microstep resolution for stepper axes. `YAA=256` sets 256 microsteps per full step on axis A. Valid powers of 2, max depends on controller model. |
| LC | Low Current Stepper Mode | Enable reduced current when stepper is holding. `LCA=1` enables on axis A. Reduces heat but lowers holding torque. |

## PID / Servo Tuning

| Code | Full Name | Description |
|------|-----------|-------------|
| KP | Proportional Constant | Proportional gain. `KPA=6` sets axis A proportional gain to 6. Active only for servo motor types. |
| KI | Integrator | Integral gain. Accumulates position error over time. Use with IL to prevent windup. |
| KD | Derivative Constant | Derivative gain. Provides damping based on rate of error change. |
| ER | Error Limit | Position error threshold in counts. If `|TP − RP| > ER`, the axis faults. `ERA=5000` sets a 5000-count error limit. Set to 0 to disable. |
| OE | Off-on-Error | Behavior when error limit is exceeded. `0`=motor stays on, `1`=motor off on error. The IOC exposes this as a PV per axis. |
| TL | Torque Limit | Continuous torque limit, range 0–9.998V of DAC output. `TLA=5.0` limits axis A to 5V output. Protects the motor/load from excessive force. |
| TK | Peak Torque Limit | Peak torque limit in same units as TL. Can exceed TL briefly during acceleration. Set to 0 to disable. |
| IL | Integrator Limit | Clamp for the integrator sum, range 0–9.998V. Prevents integral windup. Set to a value less than TL. |

## Position / Encoder

| Code | Full Name | Description |
|------|-----------|-------------|
| TP | Tell Position | Read the main encoder position. Returns counts. The IOC reads this from the data record, not via a direct TP command. |
| TD | Tell Dual Encoder | Read the auxiliary (dual) encoder position. Used when a second encoder is mounted for verification or when main/aux encoders differ. |
| DP | Define Position | Set the main encoder register to a value. `DPA=0` zeros axis A. Used during homing to define the home position. |
| DE | Dual Encoder Position | Set the auxiliary encoder register. `DEA=0` zeros the aux encoder on axis A. |
| RP | Reference Position | Read the commanded (reference) position. This is where the controller thinks the motor should be, versus TP which is where it actually is. The difference is the position error. |

## Limit and Home Switches

| Code | Full Name | Description |
|------|-----------|-------------|
| CN | Configure | Set switch polarity. `CN m,n,p` where m=limit polarity (−1 active low, 1 active high), n=home polarity, p=latch polarity. The IOC sets this from controller PVs. |
| FL | Forward Software Limit | Set forward software position limit. `FLA=100000` prevents axis A from commanding past 100000. Set to 2147483647 to disable. |
| BL | Reverse Software Limit | Set reverse software position limit. `BLA=−100000` prevents past −100000. Set to −2147483648 to disable. |
| LD | Limit Disable | Disable hardware limit switches per axis. Bit mask: bit 0=fwd limit, bit 1=rev limit. `LDA=3` disables both limits on axis A. |
| SD | Limit Switch Deceleration | Deceleration rate when a hardware limit is hit, in counts/sec². If not set, uses the DC value. |

## Motor Enable/Disable

| Code | Full Name | Description |
|------|-----------|-------------|
| SH | Servo Here | Enable the motor amplifier and close the servo loop (or energize stepper). `SHA` enables axis A. The IOC sends SH when the motor record is enabled. |
| MO | Motor Off | Open the servo loop and disable the amplifier. `MOA` disables axis A. Sent on motor record disable or when auto-off is triggered after idle timeout. |

## I/O Commands

| Code | Full Name | Description |
|------|-----------|-------------|
| SB | Set Bit | Set a digital output bit high. `SB 1` sets output bit 1. Bit numbers are 1-based. |
| CB | Clear Bit | Clear a digital output bit low. `CB 1` clears output bit 1. |
| OB | Output Bit | Set a bit to a value. `OB 1,1` sets bit 1 high; `OB 1,0` clears it. The IOC uses OB for writing digital outputs. |
| AO | Analog Output | Set analog output voltage. `AO 0,3.5` sets analog output 0 to 3.5V. Range depends on hardware (typically ±10V or 0–5V). |
| @AN | Analog Input Query | Read analog input voltage. `MG @AN[0]` reads analog input 0. The IOC reads these from the data record when available. |
| @IN | Read Digital Input | Read a digital input bit. `MG @IN[1]` reads input bit 1. Returns 0 or 1. Read from the data record at the polling rate. |
| @OUT | Read Digital Output | Read back a digital output bit. `MG @OUT[1]` reads output bit 1. |
| OC | Output Compare | Configure output compare — generates a pulse on a digital output when the encoder crosses specified positions. `OC A,1000,100` sets axis A to pulse every 100 counts starting at 1000. |

## Absolute Encoders

| Code | Full Name | Description |
|------|-----------|-------------|
| SI | Configure SSI | Configure SSI (Synchronous Serial Interface) absolute encoder. Specifies data bits, clock rate, and format. `SIA=p,q,r` where p=total bits, q=single-turn bits, r=gray/binary. |
| SS | Configure BiSS | Configure BiSS-C absolute encoder. Similar parameters to SI but for the BiSS protocol. |
| SY | BiSS Active Level | Set the active clock level for BiSS communication. `SYA=1` sets active high. |

## Communication / System

| Code | Full Name | Description |
|------|-----------|-------------|
| DR | Data Record Update Rate | Set the data record transmission interval. `DR 8000,2` sets 8ms updates on handle 2. The IOC's update period maps directly to this command. Minimum 2ms. |
| MG | Message | General-purpose query/output. The IOC uses `MG` extensively to read values that are not in the data record (e.g., `MG _MTA` reads the motor type setting). |
| ID | Identify | Return controller model and firmware version string. The IOC reads this at connect to populate the controller model PV. |
| BN | Burn | Save current parameter settings (KP, KI, ER, etc.) to EEPROM. The IOC sends BN when the burn-parameters PV is processed. |
| BP | Burn Program | Save the current DMC program to EEPROM. Sent by GalilStartController when burn option is enabled. |
| RS | Reset | Reset the controller. Clears program memory and restarts. The IOC does not normally issue RS during operation. |
| TC | Tell Error Code | Return the error code for the most recent command error. The IOC reads TC after a failed command to populate the error message PV. |
| CF | Configure Unsolicited Messages | Set which handle receives unsolicited MG output from DMC code. The IOC configures this at startup. |

## Status / Query

| Code | Full Name | Description |
|------|-----------|-------------|
| TA | Tell Amplifier Error Status | Return amplifier error status bits. Bit-mapped: overcurrent, overvoltage, overtemperature, ELO, etc. The IOC decodes these into individual amplifier status PVs. |
| TB | Tell Status Byte | Return a summary status byte for the controller. Includes bits for running, error, and command ready. |
| TS | Tell Switches | Return home, forward-limit, reverse-limit, and latch switch states as a bit field. Read from the data record at the polling rate. |
| TV | Tell Velocity | Return current actual velocity in counts/sec. Available from the data record. |
| TT | Tell Torque | Return current torque (DAC) output in volts. Available from the data record. |
| TE | Tell Error | Return current position error (TP − RP) in counts. Available from the data record. |
| SC | Stop Code | Return the reason motion stopped: `0`=running, `1`=commanded stop, `2`=fwd limit, `3`=rev limit, `4`=abort, `6`=error limit, `7`=motor off, `8`=home complete, etc. |
| QR | I/O Data Record | Request a data record immediately (synchronous mode). Used by the GalilPoller in TCP synchronous polling mode when async UDP is not available. |

## Coordinate System / Trajectory

| Code | Full Name | Description |
|------|-----------|-------------|
| CS | Clear Sequence | Clear the linear interpolation buffer for a coordinate system. `CSS` clears system S. Must be issued before loading new segments. |
| LI | Linear Interpolation Distance | Specify a segment distance for each axis. `LIA=1000,B=500` moves A 1000 and B 500 counts in the next segment. |
| LE | Linear Interpolation End | Mark the end of the interpolation sequence. The controller decelerates to a stop after this segment. |
| LM | Linear Interpolation Mode | Define which axes participate. `LMAB` assigns axes A and B to the coordinate system for linear interpolation. |
| PV | PVT Data | Specify a Position-Velocity-Time data point. `PVA=1000,500,20` means axis A should be at position 1000 with velocity 500 after 20ms. Provides smoother trajectories than LI. |
| BT | Begin PVT Motion | Begin PVT trajectory execution. Issued after loading PV segments. |
| CA | Coordinate Axes | Define axes belonging to a coordinate system. `CA ,A` adds axis A to coordinate system S. Used for vector moves and contouring. |
| VS | Vector Speed | Set vector speed for coordinated motion, in counts/sec. Applies to the resultant velocity across all axes in the coordinate system. |
| VA | Vector Acceleration | Set vector acceleration for coordinated moves, in counts/sec². |
| VD | Vector Deceleration | Set vector deceleration for coordinated moves, in counts/sec². |

## Array Commands

| Code | Full Name | Description |
|------|-----------|-------------|
| QU | Upload Array | Upload array data from controller to host. `QUA[]` uploads the entire array named A. The IOC uses this to read user arrays back from the controller. |
| DM | Dimension Array | Create a named array on the controller. `DM data[1000]` creates a 1000-element array named "data". Arrays persist until RS or power cycle (unless burned). |
| BV | Burn Variables and Array | Save all variables and arrays to EEPROM. Unlike BN (parameters only), BV saves user-created variables and array contents. |

## Error Clear

| Code | Full Name | Description |
|------|-----------|-------------|
| AZ | Clear Latched Amplifier Errors | Clear latched amplifier fault flags (overcurrent, overvoltage, etc.) so the amplifier can be re-enabled. Does not fix the underlying condition. |
