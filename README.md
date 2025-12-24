# Arduino Vehicle Yaw Control

Arduino-based vehicle control system that tracks a desired heading (yaw) and longitudinal velocity using an MPU6050 IMU, quadrature encoders, and the bicycle kinematic model. Implements PID-controlled steering and drive motors for autonomous motion.

![Vehicle control diagram]
       +---------------------+
       |    Arduino Uno      |
       +----------+----------+
                  |
        +---------v---------+
        | MPU6050 IMU (yaw) |
        +---------+---------+
                  |
        +---------v---------+
        | Bicycle Model     |
        | ψ̇ = (v/L)·tan(δ)  |
        +---------+---------+
                  |
        +---------v---------+     +------------------+
        | Yaw PD Controller | --> | Steering Motor   |
        | δ_cmd = f(ψ_error)|     | (BT7960 + Encoder)|
        +---------+---------+     +---------+--------+
                  |                         |
        +---------v---------+     +---------v--------+
        | Velocity P Ctrl   | --> | Drive Motor      |
        | v_cmd = f(v_error)|     | (BT7960 + Encoder)|
        +-------------------+     +------------------+

---

## 🛠️ Features

- **Yaw tracking** via IMU (gyro integration)
- **Steering angle control** using encoder feedback and P-controller
- **Velocity regulation** of drive wheels with encoder-based speed estimation
- **Bicycle model** converts desired yaw rate → physical steering command
- Automatic stop when heading target is reached (±1° tolerance)
- Real-time serial monitoring at **115200 baud**

---

## 📦 Hardware

- **Microcontroller**: Arduino Uno  
- **Sensors**:  
  - MPU6050 (I²C) for yaw estimation  
  - Quadrature encoders on steering & drive wheels  
- **Actuators**:  
  - Steering DC motor (BT7960 on pins 4–7)  
  - Drive DC motor (BT7960 on pins 10–13)  
- **Mechanics**: Rack-and-pinion steering, rear-wheel drive

---

## ⚙️ Dependencies

- [MPU6050_tockn](https://github.com/Tockn/MPU6050_tockn) Arduino library  
  → Install via **Sketch > Include Library > Manage Libraries...**

---

## 🔧 Configuration & Tuning

All key parameters are defined at the top of the main sketch. **Adjust these based on your hardware**:

```cpp
// Vehicle geometry
const float L = 0.27;                   // Wheelbase (m)
const float STEER_TICKS_PER_RAD = 1677; // Calibrate for your steering mechanism

// Control targets
float yawTarget = 60;   // Desired heading (degrees)
const float vTarget = 0.6; // Target speed (m/s)

// Controller gains — tune for your system’s response - TIP start with low gains and avoid adding the diffrential gain only of there are occilations 
float Ky_p = 1.2, Ky_d = 0.3;   // Yaw PD controller
float Kp_s = 12;                // Steering position P gain
float Kp_v = 250;               // Velocity P gain
