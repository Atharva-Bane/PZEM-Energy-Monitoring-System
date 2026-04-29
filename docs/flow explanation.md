# System Flow Explanation

This document explains the complete working flow of the system from data acquisition to visualization and control.

---

## 1. Data Acquisition

* The PZEM module measures electrical parameters:

  * Voltage
  * Current
  * Power
  * Energy
  * Frequency
  * Power Factor

* ESP32 reads this data via serial communication.

---

## 2. Data Transmission (ESP32 → MQTT)

* ESP32 connects to WiFi.

* It publishes the measured data to an MQTT topic:

  ```
  esp32/pzem/data
  ```

* Data is sent in real time at regular intervals.

---

## 3. MQTT Broker (Mosquitto)

* The MQTT broker acts as a message handler.
* It receives data from ESP32 and forwards it to all subscribers.

---

## 4. Backend Processing (Node.js)

* Node.js subscribes to the topic:

  ```
  esp32/pzem/data
  ```

* It processes incoming data and:

  * Sends it to the frontend
  * Stores it in a CSV file for logging

---

## 5. Frontend Visualization (React)

* The React application receives data from Node.js.
* It updates the UI in real time.
* Displays all electrical parameters dynamically.

---

## 6. Control Flow (User → Load)

### Step-by-step:

1. User clicks toggle button on the website
2. Frontend sends command to backend
3. Node.js publishes command to:

   ```
   esp32/pzem/command
   ```
4. ESP32 subscribes to this topic
5. ESP32 processes command
6. Relay module is triggered
7. Load turns ON/OFF

---

## 7. Data Logging

* Node.js continuously writes incoming data to a CSV file
* File includes timestamp + all parameters
* User can download the file via web interface

---

## Complete Flow Summary
Measurement flow:
PZEM → ESP32 → MQTT → Node.js → React UI

Control flow:
React → Node.js → MQTT → ESP32 → Relay → Load

---

## Key Insight

The system uses MQTT to decouple components, allowing seamless communication between hardware, backend, and frontend.
