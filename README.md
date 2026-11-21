# Single-Motor Drone Stabilization Testbench

A MATLAB/Simulink-based testbench for exploring sensor-based attitude
estimation and PID control using an MPU6050 and a single-motor
experimental setup.

> **Project scope:** The screenshots show the Simulink control model,
> MPU6050 sensor configuration, and an attitude-estimation MATLAB
> Function block. The exact PID gains, motor/ESC wiring, mechanical
> dimensions, and measured performance are not included here; add those
> details when you have verified them.

## Table of Contents

-   [Overview](#overview)
-   [System Architecture](#system-architecture)
-   [Hardware and Software](#hardware-and-software)
-   [Simulink Model](#simulink-model)
-   [MPU6050 Configuration](#mpu6050-configuration)
-   [Attitude Estimation](#attitude-estimation)
-   [Control Loop](#control-loop)
-   [How to Run](#how-to-run)
-   [Repository Structure](#repository-structure)
-   [Limitations and Future
    Improvements](#limitations-and-future-improvements)

## Overview

This project uses a single-motor drone stabilization testbench to study
feedback control and angular-motion measurement. An MPU6050 provides
accelerometer and gyroscope measurements. The Simulink model routes
these measurements into an attitude-estimation function, exposes Roll,
Pitch, and Yaw outputs, and includes a PID control path.

The setup is intended as an experimental platform for learning and
evaluating sensor-based attitude estimation and closed-loop control. It
should not be interpreted as a complete flight-ready drone controller.

## System Architecture

The model is organized around these functional stages:

1.  **Reference input:** A setpoint is compared with the measured
    feedback.
2.  **Error calculation:** The summing junction calculates the control
    error.
3.  **PID controller:** The error is passed to the PID block.
4.  **Sensor acquisition:** The MPU6050 block provides acceleration,
    angular-rate, temperature, and status signals.
5.  **Attitude estimation:** A MATLAB Function block processes
    accelerometer and gyroscope data and outputs Roll, Pitch, and Yaw.
6.  **Monitoring:** Sensor and attitude signals are made available
    through output ports and display/scope blocks.

### Top-level Simulink model

![Top-level Simulink model](Cse_project/Screenshot%202026-11-21%20212036.png)
*Figure 1. Top-level model showing the reference/error path, PID block,
sensor/attitude subsystem, moving-average block, and output monitoring.*

## Hardware and Software

### Hardware visible or identified in the model

-   Arduino-compatible target board (the sensor block is configured for
    Arduino).
-   MPU6050 inertial measurement unit (IMU).
-   I²C connection between the board and MPU6050.

The exact motor, ESC, power supply, and mechanical testbench
specifications are not shown in the supplied screenshots. Add the
verified part numbers and wiring details here.

### Software

-   MATLAB
-   Simulink
-   Simulink Support Package for Arduino Hardware (if deploying to an
    Arduino board)

## Simulink Model

The top-level model contains a reference signal, a summing junction, a
PID block, a sensor/attitude subsystem, signal filtering, and output
monitoring.

### Sensor and attitude subsystem

![Sensor and Attitude Subsystem](Cse_project/Screenshot%202026-11-21%20212115.png)
*Figure 2. Subsystem-level view showing the Arduino MPU6050 input and
the attitude-processing block. Roll, Pitch, Temperature, Status, and Yaw
are exposed as outputs.*

### MPU6050 and attitude-processing flow

![MPU6050 and Attitude Processing](Cse_project/Screenshot%202026-11-21%20212126.png)
*Figure 3. Detailed view of the MPU6050 sensor signals feeding the
attitude MATLAB Function block. Accelerometer and angular-rate signals
are connected to the estimator; temperature and status are routed
separately.*

## MPU6050 Configuration

The supplied block configuration shows:

  Parameter                 Configured value
  ------------------------- ------------------
  I²C module                0
  I²C address               `0x68`
  Accelerometer output      Enabled, m/s²
  Angular-rate output       Enabled, deg/s
  Temperature output        Enabled, °C
  Status output             Enabled
  Timestamp output          Disabled
  Accelerometer range       ±2G
  Gyroscope range           250 dps
  Accelerometer bandwidth   94 Hz
  Gyroscope bandwidth       98 Hz
  Samples per frame         1
  Sample time               `-1` (inherited)

![MPU6050 block
configuration](Cse_project/Screenshot%2026-11-21%212142.png)

*Figure 4. MPU6050 I²C and sensor-range configuration in Simulink.*

## Attitude Estimation

The attitude subsystem uses a MATLAB Function block named `attitude`.
Its visible interface accepts:

-   `Accel` --- accelerometer measurement
-   `AngRate` --- gyroscope angular-rate measurement

It provides:

-   `Roll`
-   `Pitch`
-   `Yaw`

The supplied screenshots establish the function's interface and signal
connections, but do not show the function's internal source code or the
specific estimation algorithm. Document the actual algorithm (for
example, complementary filter or Kalman filter) only after confirming
the implementation.

![Attitude MATLAB Function
block](assets/screenshots/05-attitude-matlab-function-block.png)

*Figure 5. Attitude MATLAB Function block and its Roll, Pitch, and Yaw
outputs.*

## Control Loop

The top-level diagram shows a reference signal being compared against a
feedback signal. The resulting error is sent to a PID block, whose
output is connected to the sensor/plant subsystem.

For a complete technical description, add the following verified
implementation details:

-   Controlled axis (Roll, Pitch, or another angle)
-   Setpoint units and range
-   PID gains (`Kp`, `Ki`, `Kd`)
-   PID sample time
-   Motor command limits and ESC pulse-width mapping
-   Saturation and anti-windup configuration, if used
-   Feedback signal and sign convention

Do not assume that the visible constant values are PID gains; they are
signal constants in the displayed model.

## How to Run

1.  Install MATLAB and Simulink, along with the Arduino support package
    required by your target board.
2.  Connect the MPU6050 to the board using the I²C pins and the correct
    power and ground connections for your hardware.
3.  Open the project `.slx` model in MATLAB/Simulink.
4.  Confirm the selected Arduino board, I²C module, sensor address, and
    sensor settings.
5.  Check that the attitude function's input dimensions, units, and
    sample time match the MPU6050 block outputs.
6.  Configure the PID parameters and output limits for your actual
    testbench.
7.  Run the model in simulation or deploy it to the target hardware, as
    appropriate.
8.  Monitor Roll, Pitch, Yaw, sensor status, and the control response.

**Safety:** Test the motor with the propeller removed during initial
electrical and control checks. Secure the testbench, verify the power
system and ESC limits, and keep clear of rotating components. Do not use
this experimental setup for free flight without a separate safety and
flight-readiness review.

## Repository Structure

``` text
.
├── README.md
├── your-simulink-model.slx
└── assets/
    └── screenshots/
        ├── 01-simulink-top-level-model.png
        ├── 02-sensor-and-attitude-subsystem.png
        ├── 03-mpu6050-and-attitude-function.png
        ├── 04-mpu6050-configuration.png
        └── 05-attitude-matlab-function-block.png
```

Replace `your-simulink-model.slx` with the actual model filename when
you add it to the repository.

## Limitations and Future Improvements

-   Add the full MATLAB Function source code and describe the
    implemented attitude-estimation method.
-   Document the motor, ESC, power supply, sensor wiring, and mechanical
    testbench.
-   Record PID gains, sampling frequency, setpoint, and actuator limits.
-   Add plots of setpoint versus measured angle, control output, and
    stabilization response.
-   Measure and report steady-state error, settling time, overshoot, and
    response under disturbances.
-   Add calibration, sensor-bias handling, filtering, and fault/status
    checks where applicable.
-   Validate the complete system under controlled and safe test
    conditions.
