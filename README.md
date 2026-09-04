# Smart Dustbin – Human Detection + Fill Level Monitoring

This project implements an automated dustbin that opens its lid when a person approaches and monitors the fill level using two ultrasonic sensors. Data is published to **Adafruit IO** for remote monitoring and visualization. The system is built for the **ESP32** and can be simulated in **Wokwi** or deployed on real hardware.

## Features

- **Human detection** – Ultrasonic sensor detects a person within a configurable distance (30 cm) and opens the servo‑controlled lid.
- **Auto‑close** – Lid closes automatically after 5 seconds (configurable).
- **Fill level monitoring** – Second ultrasonic sensor measures the remaining distance to the trash, calculates fill percentage.
- **Visual indicators** – Green LED indicates fill ≤ 70%, Red LED indicates fill > 70% (configurable threshold).
- **Cloud integration** – Publishes human presence, fill percentage, and lid status to Adafruit IO feeds.
- **Configurable LED polarity** – Supports both active‑high and active‑low wiring via a simple macro.

## Components Required

| Component                | Quantity |
|--------------------------|----------|
| ESP32 development board  | 1        |
| HC‑SR04 Ultrasonic sensor| 2        |
| Servo motor (e.g., SG90) | 1        |
| Green LED                | 1        |
| Red LED                  | 1        |
| Resistors (220 Ω)        | 2        |
| Breadboard & jumper wires| as needed|

> *Note:* The code is also fully compatible with the Wokwi online simulator.

## Pin Connections

| Component               | GPIO Pin |
|-------------------------|----------|
| Ultrasonic 1 (Human) – TRIG | 5    |
| Ultrasonic 1 (Human) – ECHO | 18   |
| Ultrasonic 2 (Fill) – TRIG   | 19   |
| Ultrasonic 2 (Fill) – ECHO   | 21   |
| Servo signal wire           | 13   |
| Green LED (anode)           | 25   |
| Red LED (anode)             | 26   |

- **LED Wiring**: The code is written to work with either common‑cathode (active‑high) or common‑anode (active‑low) configurations. Set the `LED_ACTIVE_HIGH` macro accordingly (see *Configuration* below).

## How It Works

1. **Human Detection**  
   The first ultrasonic sensor continuously measures the distance. If a person is detected within `HUMAN_DETECT_CM` (default 30 cm) and the lid is currently closed, the servo rotates to 90° (open) and a timer starts. After `SERVO_OPEN_TIME` (5 seconds), the lid closes automatically.

2. **Fill Level Calculation**  
   The second ultrasonic sensor measures the distance from the sensor to the top of the trash. The fill percentage is calculated as:  
   `fill% = ((BIN_HEIGHT_CM - measured_distance) / BIN_HEIGHT_CM) * 100`  
   If the reading is invalid, the fill is assumed to be 0%.

3. **LED Indication**  
   - Fill ≤ `FILL_THRESHOLD` (70%): **Green LED ON**, Red LED OFF  
   - Fill > `FILL_THRESHOLD`: **Red LED ON**, Green LED OFF

4. **Adafruit IO Publishing**  
   Every 10 seconds (`PUBLISH_INTERVAL`), the following data is sent:
   - `human-detected` – 1 if lid is open (person present), else 0
   - `dustbin-level` – fill percentage (0–100)
   - `lid-status` – text "OPEN" or "CLOSED"

## Setup Instructions

### 1. Install Required Libraries

Open the Arduino IDE and install these libraries via the Library Manager:

- **ESP32Servo** (by Kevin Harrington)
- **Adafruit IO Arduino** (by Adafruit)

### 2. Configure WiFi and Adafruit IO Credentials

In the code, replace the following definitions with your own:

```cpp
#define WIFI_SSID   "your-ssid"
#define WIFI_PASS   "your-password"
#define IO_USERNAME "your-adafruit-io-username"
#define IO_KEY      "your-adafruit-io-key"
