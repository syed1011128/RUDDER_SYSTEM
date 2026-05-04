# 🚢 Arduino Rudder Control System

![Arduino](https://img.shields.io/badge/Arduino-Uno-00979D?style=for-the-badge&logo=arduino&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Arduino-blue?style=for-the-badge)

An **obstacle-aware rudder control system** built with Arduino Uno, a servo motor, and an ultrasonic sensor. The system continuously monitors the distance to objects in front and automatically adjusts the rudder (servo) angle to steer away from obstacles — simulating a real-world autonomous marine or vehicle steering mechanism.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Hardware Components](#hardware-components)
- [Circuit Diagram](#circuit-diagram)
- [Pin Configuration](#pin-configuration)
- [How It Works](#how-it-works)
- [Installation & Setup](#installation--setup)
- [Code](#code)
- [Project Structure](#project-structure)
- [Customization](#customization)
- [Troubleshooting](#troubleshooting)
- [Future Improvements](#future-improvements)
- [License](#license)

---

## 🔍 Overview

This project demonstrates a **basic autonomous rudder system** that uses an ultrasonic sensor to detect nearby obstacles and commands a servo motor to adjust the steering direction accordingly. It is ideal for:

- Beginner to intermediate Arduino learners
- Robotics and embedded systems prototypes
- RC boat / vehicle steering experiments
- Educational demonstrations of sensor-actuator integration

---

## ✨ Features

- 📡 Real-time distance measurement using the HC-SR04 ultrasonic sensor
- ⚙️ Automatic servo (rudder) adjustment based on obstacle proximity
- 🔁 Continuous loop — no manual input required
- 🧱 Simple breadboard circuit — no soldering needed
- 🪛 Fully customizable threshold distances and servo angles
- 📟 Serial Monitor output for live debugging and distance readings

---

## 🧰 Hardware Components

| # | Component              | Quantity | Description                                      |
|---|------------------------|----------|--------------------------------------------------|
| 1 | Arduino Uno            | 1        | Main microcontroller board                       |
| 2 | Servo Motor            | 1        | Controls the rudder angle (e.g., SG90 or MG996R)|
| 3 | HC-SR04 Ultrasonic Sensor | 1     | Measures distance to obstacles                   |
| 4 | Breadboard             | 1        | For prototyping connections without soldering     |
| 5 | Jumper Wires           | ~10–15   | Male-to-male for connecting components           |
| 6 | USB Cable (Type-B)     | 1        | For uploading code and powering the Arduino      |

> **Optional:** 9V battery or external 5V power supply for standalone operation.

---

## 🔌 Circuit Diagram

```
                    Arduino Uno
                  ┌─────────────┐
                  │             │
    HC-SR04       │         5V  ├──── VCC (Ultrasonic + Servo)
    ┌──────┐      │        GND  ├──── GND (Common Ground)
    │ VCC  ├──────┤             │
    │ GND  ├──────┤         D9  ├──── Servo Signal Wire (Orange/Yellow)
    │ TRIG ├──────┤         D7  ├──── HC-SR04 TRIG
    │ ECHO ├──────┤         D6  ├──── HC-SR04 ECHO
    └──────┘      │             │
                  └─────────────┘

    Servo Motor
    ┌──────────────────┐
    │ Red   ──── 5V    │
    │ Brown ──── GND   │
    │ Orange──── D9    │
    └──────────────────┘
```

> All components share a common **GND** on the breadboard rail connected to the Arduino's GND pin.

---

## 📌 Pin Configuration

| Component        | Arduino Pin | Wire Color (Typical) | Notes                          |
|------------------|-------------|----------------------|--------------------------------|
| HC-SR04 VCC      | 5V          | Red                  | Power supply                   |
| HC-SR04 GND      | GND         | Black                | Common ground                  |
| HC-SR04 TRIG     | Digital 7   | Yellow               | Trigger pulse output           |
| HC-SR04 ECHO     | Digital 6   | Green                | Echo pulse input               |
| Servo Signal     | Digital 9   | Orange / Yellow      | PWM signal for angle control   |
| Servo VCC        | 5V          | Red                  | Servo power                    |
| Servo GND        | GND         | Black / Brown        | Common ground                  |

---

## ⚙️ How It Works

The system operates in a continuous loop following these steps:

```
[Start]
   │
   ▼
[Send 10µs TRIG pulse from Arduino → HC-SR04]
   │
   ▼
[HC-SR04 emits 8 ultrasonic bursts at 40kHz]
   │
   ▼
[ECHO pin goes HIGH → Measure pulse duration]
   │
   ▼
[Calculate Distance = (Duration × 0.034) / 2  (in cm)]
   │
   ▼
[Is distance < THRESHOLD (e.g., 20 cm)?]
   │
   ├── YES → Steer Away (rotate servo to avoid angle)
   │
   └── NO  → Stay Straight (servo at center / 90°)
   │
   ▼
[Print distance to Serial Monitor]
   │
   ▼
[Delay 100ms → Repeat loop]
```

### Distance Formula

```
Distance (cm) = (Echo Pulse Duration in µs × Speed of Sound) / 2
             = (duration × 0.034) / 2
```

The division by 2 accounts for the round-trip of the sound wave (to obstacle and back).

---

## 🚀 Installation & Setup

### Prerequisites

- [Arduino IDE](https://www.arduino.cc/en/software) (version 1.8+ or 2.x)
- A USB cable to connect Arduino Uno to your computer
- The `Servo.h` library (built into Arduino IDE — no extra installation needed)

### Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/arduino-rudder-system.git
   cd arduino-rudder-system
   ```

2. **Open the sketch**
   - Launch Arduino IDE
   - Go to `File → Open`
   - Select `rudder_system.ino` from the project folder

3. **Connect your hardware**
   - Follow the [Pin Configuration](#pin-configuration) table above
   - Double-check all wires on the breadboard before powering on

4. **Select the correct board and port**
   - Go to `Tools → Board → Arduino AVR Boards → Arduino Uno`
   - Go to `Tools → Port → COMX` (Windows) or `/dev/ttyUSBX` (Linux/Mac)

5. **Upload the sketch**
   - Click the **Upload** button (→) or press `Ctrl + U`
   - Wait for "Done uploading" in the status bar

6. **Open Serial Monitor** (optional, for debugging)
   - Press `Ctrl + Shift + M` or go to `Tools → Serial Monitor`
   - Set baud rate to **9600**

---

## 💻 Code

```cpp
#include <Servo.h>

// ─── Pin Definitions ──────────────────────────────────────────
const int TRIG_PIN    = 7;    // HC-SR04 Trigger pin
const int ECHO_PIN    = 6;    // HC-SR04 Echo pin
const int SERVO_PIN   = 9;    // Servo motor signal pin

// ─── Configuration ────────────────────────────────────────────
const int THRESHOLD_DISTANCE = 20;  // Distance in cm to trigger steering
const int CENTER_ANGLE       = 90;  // Straight-ahead rudder angle
const int AVOID_ANGLE        = 45;  // Steering angle when obstacle detected
const int LOOP_DELAY         = 100; // Delay between measurements (ms)

Servo rudderServo;

// ─── Setup ────────────────────────────────────────────────────
void setup() {
  Serial.begin(9600);
  
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  
  rudderServo.attach(SERVO_PIN);
  rudderServo.write(CENTER_ANGLE);  // Start centered
  
  Serial.println("=== Rudder Control System Started ===");
  delay(1000);
}

// ─── Distance Measurement ─────────────────────────────────────
long measureDistance() {
  // Send 10µs trigger pulse
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  
  // Read echo duration and calculate distance
  long duration = pulseIn(ECHO_PIN, HIGH, 30000); // 30ms timeout
  long distance  = (duration * 0.034) / 2;
  
  return distance;
}

// ─── Main Loop ────────────────────────────────────────────────
void loop() {
  long distance = measureDistance();
  
  // Print to Serial Monitor
  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");
  
  // Steer based on obstacle proximity
  if (distance > 0 && distance < THRESHOLD_DISTANCE) {
    Serial.println("⚠️  Obstacle detected! Steering away...");
    rudderServo.write(AVOID_ANGLE);
  } else {
    rudderServo.write(CENTER_ANGLE);
  }
  
  delay(LOOP_DELAY);
}
```

> **Note:** A distance of `0` from `pulseIn()` indicates a timeout (no echo received), which typically means the obstacle is either too far away or out of sensor range. The code handles this by only triggering the avoid action when `distance > 0`.

---

## 📁 Project Structure

```
arduino-rudder-system/
│
├── rudder_system.ino       # Main Arduino sketch
├── README.md               # Project documentation (this file)
├── circuit/
│   └── circuit_diagram.png # Wiring diagram image (add your own)
└── docs/
    └── notes.md            # Additional notes or references
```

---

## 🎛️ Customization

You can easily tune the system by modifying the constants at the top of the sketch:

| Constant              | Default | Description                                                   |
|-----------------------|---------|---------------------------------------------------------------|
| `THRESHOLD_DISTANCE`  | `20`    | Distance (cm) at which the rudder begins to steer away       |
| `CENTER_ANGLE`        | `90`    | Servo angle for straight-ahead navigation                     |
| `AVOID_ANGLE`         | `45`    | Servo angle when an obstacle is detected                      |
| `LOOP_DELAY`          | `100`   | Delay in milliseconds between sensor readings                 |
| `TRIG_PIN`            | `7`     | Change if using a different digital pin for TRIG              |
| `ECHO_PIN`            | `6`     | Change if using a different digital pin for ECHO              |
| `SERVO_PIN`           | `9`     | Change if using a different PWM pin for the servo             |

**Example:** To steer right instead of left when an obstacle is found, change `AVOID_ANGLE` from `45` to `135`.

---

## 🛠️ Troubleshooting

| Problem                        | Possible Cause                              | Solution                                                   |
|-------------------------------|---------------------------------------------|------------------------------------------------------------|
| Servo not moving              | Incorrect pin or loose wire                 | Check servo signal wire is on pin 9 and wired correctly    |
| Distance always reads 0       | TRIG/ECHO pins swapped or loose             | Verify wiring matches pin config table                     |
| Erratic servo behavior        | Insufficient power from USB                 | Use external 5V power supply for the servo                 |
| Serial Monitor shows garbage  | Wrong baud rate selected                    | Set Serial Monitor to **9600 baud**                        |
| Sensor reads incorrectly      | Object too close (<2 cm) or too far (>4 m) | HC-SR04 works best between **2 cm and 400 cm**             |
| Upload fails                  | Wrong port or board selected                | Re-select correct COM port and "Arduino Uno" board         |

---

## 🔮 Future Improvements

- [ ] Add a second ultrasonic sensor for left/right detection
- [ ] Implement proportional steering (smoother angle based on distance)
- [ ] Add an LCD display to show live distance readings
- [ ] Integrate a buzzer for audio obstacle alerts
- [ ] Add a manual override button to override autonomous mode
- [ ] Migrate to wireless control using an HC-05 Bluetooth module
- [ ] Power the system with a LiPo battery for full portability
- [ ] Build a physical rudder and mount to a model boat

---

## 📄 License

This project is licensed under the **MIT License** — you are free to use, modify, and distribute it with attribution.

```
MIT License

Copyright (c) 2025 [Your Name]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction...
```

See the [LICENSE](LICENSE) file for full details.

---

## 🙌 Acknowledgements

- [Arduino Official Documentation](https://www.arduino.cc/reference/en/)
- [HC-SR04 Datasheet](https://cdn.sparkfun.com/datasheets/Sensors/Proximity/HCSR04.pdf)
- [Arduino Servo Library Reference](https://www.arduino.cc/reference/en/libraries/servo/)

---

> **Made with ❤️ using Arduino Uno | Feel free to ⭐ star this repo if it helped you!**