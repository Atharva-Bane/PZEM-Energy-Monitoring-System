# System Evolution

This document captures how the project evolved from initial concept to final implementation.

---

## Initial Concept

The project started as an IoT-based energy monitoring system with the following planned architecture:

PZEM → ESP32 → Raspberry Pi → Web Dashboard

The Raspberry Pi was intended to handle backend processing, download broker in it for MQTT connection.

---

## Issue with Raspberry Pi

During implementation, the Raspberry Pi 5 faced significant WiFi connectivity issues.
Due to which I had to again erase & rewrite all the progress I made earlier.

Also if you are an IPhone user, it won't connect at all. Use WiFi hotspot of an Android user.

### Problems observed:

* Raspberry Pi won't connect to the WiFi SSID provided during its setup (even for Android hotspot) sometimes
* Difficulty maintaining consistent communication

---

## Major Design Pivot

Due to reliability concerns, the architecture was redesigned.

### Decision:

Remove Raspberry Pi entirely and shift to a fully ESP32-based system.

---

## Updated Architecture

ESP32 → MQTT Broker (Mosquitto) → Node.js → React Frontend

---

## Benefits of New Design

* Reduced system complexity
* Improved reliability
  ESP32 could readily connect to hotspot provided in its IDE code setup
* Lower hardware dependency
* Faster communication
* Easier debugging

---

## Hardware Evolution

### Initial Issue:

The PZEM module used a coil-based sensing mechanism instead of a plug-and-play interface.

### Problem:

* Difficult to securely position load wires
* Inconvenient for replacing loads

### Solution:

* Designed a socket based approach
* Allowed easy insertion and removal of load wires
* Improved safety and usability
This update played a special role. It impressed the professor too.

---

## Software Evolution

### MQTT Integration

* Initial connection issues were faced
* Fixed through correct broker configuration and topic management
  You can use MQTTX for reliable connection and easier debugging

---

### Relay Control Bug

* Relay behavior was inverted (ON/OFF mismatch)

### Fix:

* Adjusted control logic in ESP32 firmware

---

## Final System Capabilities

* Real time monitoring of electrical parameters
* Remote control of load
* Stable MQTT based communication
* Data logging and CSV download

---

## Key Takeaway

The project significantly improved after simplifying the architecture and focusing on reliability over complexity.
