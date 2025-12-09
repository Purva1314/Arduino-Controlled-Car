# 🤖 Hand-Gesture Controlled Robot Car (MPU6050 & nRF24L01)

A complete hardware and software project for controlling a mobile robot platform using hand gestures captured by an **MPU6050** sensor worn on a glove. Communication is handled wirelessly using **nRF24L01** transceivers.



---

## ✨ Features

* **Intuitive Control:** Control movement (Forward, Backward, Left, Right, Stop) simply by tilting the hand.
* **Wireless Communication:** Reliable data transmission using the **nRF24L01** module.
* **Modular Design:** Separate, clearly defined codebases for the **Transmitter (Glove)** and **Receiver (Car)**.
* **Safety Stop:** The robot automatically defaults to a stop command if the hand is level or if the wireless signal is lost.

---

## 🛠️ Components Required

### 1. 🧤 Transmitter (Glove Unit)

| Component | Quantity | Notes |
| :--- | :---: | :--- |
| **Arduino Nano** or Uno | 1 | Power-efficient for wearable use. |
| **MPU6050** | 1 | 6-Axis Accelerometer and Gyroscope. |
| **nRF24L01** Module | 1 | 2.4 GHz Transceiver. |
| **3.7V Li-Po Battery** | 1 | Power source for the glove unit. |
| **Glove** | 1 | For mounting the sensor. |

### 2. 🚗 Receiver (Car Unit)

| Component | Quantity | Notes |
| :--- | :---: | :--- |
| **Arduino Uno** | 1 | Main controller for the robot. |
| **L298N** Motor Driver | 1 | To drive high-current DC motors. |
| **nRF24L01** Module | 1 | 2.4 GHz Transceiver. |
| **DC Gear Motors** | 4 (or 2) | For propulsion. |
| **Chassis/Wheels** | 1 Set | Basic 4-wheel robot chassis. |
| **External Power** | 1 | 7V-12V battery pack for motors. |

---

## 🔗 Wiring and Schematics

### 1. Transmitter (Glove) Schematic

The MPU6050 connects via **I2C**, and the nRF24L01 connects via **SPI**.

| Connection | MPU6050 | nRF24L01 | Arduino Nano |
| :--- | :---: | :---: | :---: |
| **I2C SDA** | SDA | - | A4 |
| **I2C SCL** | SCL | - | A5 |
| **SPI CE** | - | CE | D9 |
| **SPI CSN** | - | CSN | D10 |
| **SPI MOSI** | - | MOSI | D11 |
| **SPI MISO** | - | MISO | D12 |
| **SPI SCK** | - | SCK | D13 |



---

### 2. Receiver (Car) Schematic

The L298N driver handles motor power, and the nRF24L01 handles communication.

| Connection | L298N Driver | nRF24L01 | Arduino Uno |
| :--- | :---: | :---: | :---: |
| **Motor IN1** | IN1 | - | D4 |
| **Motor IN2** | IN2 | - | D5 |
| **Motor IN3** | IN3 | - | D6 |
| **Motor IN4** | IN4 | - | D7 |
| **Enable A (Speed)** | ENA | - | D8 (PWM) |
| **Enable B (Speed)** | ENB | - | D11 (PWM) |
| **SPI CE** | - | CE | D9 |
| **SPI CSN** | - | CSN | D10 |



---

## ⚙️ Software and Setup

### 1. Libraries

Install the following libraries in your Arduino IDE:

* **MPU6050:** The **Wire.h** library (built-in) is used for basic I2C communication.
* **nRF24L01:** The **RF24** library by TMRh20.
* **SPI:** The **SPI.h** library (built-in).

### 2. Code Upload

The repository contains two main files:

| File | Description | Upload To |
| :--- | :--- | :--- |
| `Transmitter_Glove.ino` | Reads MPU6050 data and sends commands (F, B, L, R, S). | **Arduino Nano** (Glove) |
| `Receiver_Car.ino` | Listens for commands and controls the L298N motor driver. | **Arduino Uno** (Car) |

**⚠️ IMPORTANT:** Ensure the radio pipe address (`const byte addresses[][6] = {"00001", "00002"};`) is identical in both the transmitter and receiver sketches.

---

## 💻 Control Logic (Gesture Mapping)

The transmitter code translates the accelerometer readings (AccX for forward/backward, AccY for left/right) into a simple character command:

| Gesture | MPU6050 Condition | Command Sent | Car Action |
| :--- | :--- | :--- | :--- |
| **Palm Down** | AccX $<$ -4000 | `F` | Forward |
| **Palm Up** | AccX $>$ 4000 | `B` | Backward |
| **Tilt Left** | AccY $>$ 4000 | `L` | Turn Left |
| **Tilt Right** | AccY $<$ -4000 | `R` | Turn Right |
| **Palm Flat** | All within range | `S` | Stop |

**Tip:** You may need to **calibrate the threshold values** (e.g., `4000`) in the `Transmitter_Glove.ino` file based on the orientation and sensitivity of your specific MPU6050 module on the glove.
