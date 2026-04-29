# System Architecture

This document explains the complete architecture of the PZEM Energy Monitoring System, including data flow, control flow, and design decisions.

---

## Overall Architecture

The system follows a distributed IoT architecture:

ESP32 → MQTT Broker (Mosquitto) → Node.js → React Frontend

---

## Data Flow (Monitoring)

1. The PZEM module measures electrical parameters:

   * Voltage
   * Current
   * Power
   * Energy
   * Frequency
   * Power Factor

2. ESP32 reads this data via serial communication.

3. ESP32 publishes the data to an MQTT topic:

   ```
   esp32/pzem/data
   ```

4. The Mosquitto broker routes this data.

5. Node.js subscribes to `esp32/pzem/data` and processes incoming messages.

6. The processed data is sent to the React frontend via WebSocket/HTTP.

7. The frontend updates the UI in real-time.

---

## 🔌 Control Flow (Load Switching)

1. User interacts with toggle button on the website.

2. React frontend sends command to backend.

3. Node.js publishes command to MQTT topic:

   ```
   esp32/pzem/command
   ```

4. ESP32 subscribes to `esp32/pzem/command`.

5. Based on command:

   * HIGH → Relay ON
   * LOW → Relay OFF

6. Relay switches the connected load.

---

## 📊 Data Logging System

* Node.js stores incoming data into a CSV file.
* Each entry contains timestamp + measured parameters.
* Users can download the CSV file from the web interface.

---

## Design Decisions

### Why MQTT?

* Lightweight protocol
* Real time communication
* Decouples hardware and frontend

---

### Why ESP32 instead of Raspberry Pi?

* Lower complexity
* More stable WiFi in this setup
* Reduced system dependency

---

### Why Node.js?

* Efficient handling of asynchronous data
* Easy integration with MQTT and frontend

---

## System Advantages

* Modular architecture
* Scalable (trying to add multiple sensors)
* Real time + historical data
* Remote control capability

---

## Limitations

* Local network dependency
* No cloud backup (currently)
* Single node system (can be expanded)
