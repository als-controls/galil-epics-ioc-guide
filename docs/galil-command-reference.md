# Appendix: Galil Command Reference

Quick reference of Galil 2-letter commands used by the IOC. Full documentation is available in the Galil Command Reference manual from Galil Motion Control.

## Motion Commands

| Code | Full Name | Description |
|------|-----------|-------------|
| BG | Begin | Begin motion on specified axis or coordinate system |
| ST | Stop | Stop motion on specified axis |
| PA | Position Absolute | Move to absolute position |
| PR | Position Relative | Move relative distance |
| JG | Jog | Jog at specified velocity |
| SP | Speed | Set speed for independent moves |
| AC | Acceleration | Set acceleration rate |
| DC | Deceleration | Set deceleration rate |
| IT | Independent Time Constant | Smoothing time constant for independent moves |
| HM | Home | Begin home sequence |
| FI | Find Index | Find encoder index pulse |
| FE | Find Edge | Find home switch edge |
| BG S / BG T | Begin Coordinate | Begin coordinated motion on S or T system |
| ST S / ST T | Stop Coordinate | Stop coordinated motion |

## Motor Configuration

| Code | Full Name | Description |
|------|-----------|-------------|
| MT | Motor Type | Set motor type (servo, stepper, etc.) |
| CE | Configure Encoder | Configure encoder type (quadrature, pulse/dir) |
| BR | Brush Axis | Set brush type (brushed, brushless, external) |
| AG | Amplifier Gain | Set amplifier gain |
| AU | Set Amplifier Current Loop | Configure current loop gain |
| KS | Step Motor Smoothing | Smoothing factor for stepper motors |
| YA | Step Drive Resolution | Set microstep resolution |
| LC | Low Current Stepper Mode | Low current hold mode for steppers |

## PID / Servo Tuning

| Code | Full Name | Description |
|------|-----------|-------------|
| KP | Proportional Constant | PID proportional gain |
| KI | Integrator | PID integral gain |
| KD | Derivative Constant | PID derivative gain |
| ER | Error Limit | Position error limit before fault |
| OE | Off-on-Error | Motor off on error behavior |
| TL | Torque Limit | Continuous torque limit |
| TK | Peak Torque Limit | Peak torque limit |
| IL | Integrator Limit | Limit for integrator term |

## Position / Encoder

| Code | Full Name | Description |
|------|-----------|-------------|
| TP | Tell Position | Report main encoder position |
| TD | Tell Dual Encoder | Report auxiliary/dual encoder position |
| DP | Define Position | Set main encoder register |
| DE | Dual Encoder Position | Set auxiliary encoder register |
| RP | Reference Position | Report commanded reference position |

## Limit and Home Switches

| Code | Full Name | Description |
|------|-----------|-------------|
| CN | Configure | Configure limit/home switch polarity |
| FL | Forward Software Limit | Set forward software limit position |
| BL | Reverse Software Limit | Set reverse software limit position |
| LD | Limit Disable | Disable hardware limits per axis |
| SD | Limit Switch Deceleration | Deceleration rate on limit switch |

## Motor Enable/Disable

| Code | Full Name | Description |
|------|-----------|-------------|
| SH | Servo Here | Enable motor (close servo loop) |
| MO | Motor Off | Disable motor (open servo loop) |

## I/O Commands

| Code | Full Name | Description |
|------|-----------|-------------|
| SB | Set Bit | Set digital output bit high |
| CB | Clear Bit | Clear digital output bit low |
| OB | Output Bit | Set output bit to specified state |
| AO | Analog Output | Set analog output voltage |
| @AN | Analog Input Query | Read analog input value |
| @IN | Read Digital Input | Read digital input bit |
| @OUT | Read Digital Output | Read digital output bit |
| OC | Output Compare | Configure output compare (pulse on encoder position) |

## Absolute Encoders

| Code | Full Name | Description |
|------|-----------|-------------|
| SI | Configure SSI | Configure SSI absolute encoder |
| SS | Configure BiSS | Configure BiSS absolute encoder |
| SY | BiSS Active Level | Set BiSS encoder active level |

## Communication / System

| Code | Full Name | Description |
|------|-----------|-------------|
| DR | Data Record Update Rate | Configure data record transmission rate and handle |
| MG | Message | Query a value or send message. General-purpose read |
| ID | Identify | Return controller model and firmware info |
| BN | Burn | Save parameters to EEPROM |
| BP | Burn Program | Save program to EEPROM |
| RS | Reset | Reset controller |
| TC | Tell Error Code | Return error code for last error |
| CF | Configure Unsolicited Messages | Configure message routing handle |

## Status / Query

| Code | Full Name | Description |
|------|-----------|-------------|
| TA | Tell Amplifier Error Status | Return amplifier error status bits |
| TB | Tell Status Byte | Return status byte |
| TS | Tell Switches | Return home/limit switch states |
| TV | Tell Velocity | Return current velocity |
| TT | Tell Torque | Return current torque output |
| TE | Tell Error | Return position error |
| SC | Stop Code | Return stop code (reason motion stopped) |
| QR | I/O Data Record | Request data record (used in sync polling) |

## Coordinate System / Trajectory

| Code | Full Name | Description |
|------|-----------|-------------|
| CS | Clear Sequence | Clear linear interpolation sequence |
| LI | Linear Interpolation Distance | Specify segment in linear interpolation |
| LE | Linear Interpolation End | Mark end of linear interpolation sequence |
| LM | Linear Interpolation Mode | Set axes for linear interpolation |
| PV | PVT Data | Specify PVT (Position-Velocity-Time) data point |
| BT | Begin PVT Motion | Begin PVT trajectory |
| CA | Coordinate Axes | Define axes in coordinate system |
| VS | Vector Speed | Set vector speed for coordinated motion |
| VA | Vector Acceleration | Set vector acceleration |
| VD | Vector Deceleration | Set vector deceleration |

## Array Commands

| Code | Full Name | Description |
|------|-----------|-------------|
| QU | Upload Array | Upload array from controller |
| DM | Dimension Array | Create array on controller |
| BV | Burn Variables and Array | Save variables and arrays to EEPROM |

## Error Clear

| Code | Full Name | Description |
|------|-----------|-------------|
| AZ | Clear Latched Amplifier Errors | Clear amplifier error flags |
