# PZEM-Energy-Monitoring-System
IoT-based real-time energy monitoring system using ESP32, MQTT, and web dashboard
# ⚡ PZEM Energy Monitoring System

An end-to-end IoT-based real-time energy monitoring and control system built using ESP32, MQTT, Node.js, and a React web dashboard.

---

## Overview

This project is designed to monitor electrical parameters of a load in real-time and control it remotely via a web interface.

The system measures:

* Voltage
* Current
* Power
* Energy
* Frequency
* Power Factor

All data is transmitted wirelessly and visualized on a live dashboard, with control capability integrated into the same interface.

---

## System Architecture

ESP32 → MQTT Broker (Mosquitto) → Node.js Server → React Frontend

---

## Working Principle

1. The ESP32 reads electrical parameters from the PZEM module.
2. The data is published to an MQTT topic via WiFi.
3. The Mosquitto MQTT broker handles message routing.
4. Node.js subscribes to the MQTT topic and processes incoming data.
5. The processed data is sent to the React frontend.
6. The frontend displays real-time values on the website.

### Remote Control

* The web dashboard includes a toggle button.
* When triggered, it publishes a command to a separate MQTT topic.
* ESP32 subscribes to this command topic.
* Based on the received command, ESP32 controls the relay module.
* The relay switches the connected load ON/OFF.

---

## Data Logging

* System logs energy data into a CSV file.
* Users can download historical data directly from the web interface.
* Enables offline analysis and tracking of energy consumption.

---

## Tech Stack

### Hardware

* ESP32
* PZEM Energy Meter Module
* Relay Module
* Electrical Load

### Software

* MQTT (Mosquitto Broker)
* Node.js Backend
* React Frontend

---

## Key Features

* Real time monitoring of electrical parameters
* Wireless data transmission using MQTT
* Interactive web dashboard
* Remote control of electrical load
* Historical data logging and CSV export
* Fully integrated hardware + software system

---

## Challenges & Problem Solving

### Raspberry Pi Integration Issue

Initially, the system was designed to include a Raspberry Pi 5 for handling backend operations. However, unstable WiFi connectivity posed a reliability risk.

**Solution:**
Shifted to a fully ESP32-based architecture for improved stability and simplicity.

---

### MQTT Communication Issues

Faced connection and data transmission inconsistencies during initial setup.

**Solution:**
Resolved through correct broker configuration, port handling, and debugging network settings.

---

### Relay Logic Inversion Bug

The relay behavior was inverted (ON/OFF logic mismatch).

**Solution:**
Corrected logic at firmware level to ensure proper control behavior.

---

### Hardware Integration Challenge (PZEM Coil)

The PZEM module used a coil-based sensing mechanism instead of a plug-and-play connector.

**Problem:**
Difficult to securely place and replace load wires.

**Solution:**
Designed a socket-based interface to:

* Securely hold wires inside the coil
* Allow easy load replacement
* Improve safety and usability

---

## Future Improvements

* Scaling up the project for multiple loads using decoder logic 
* Cloud integration (AWS / Firebase)
* Mobile app interface
* Advanced analytics and visualization
* Alert system for abnormal power usage
* PCB design for compact integration

---

## What I Would Do Differently

* Plan hardware interfacing earlier to avoid redesign
* Test communication modules independently before integration
* Implement modular architecture from the beginning

---


## Author

Atharva Bane
B.Tech Electronics (Minor in Aerospace Engineering) at VJTI,Mumbai

---

## Final Note

This project represents a complete IoT system integrating hardware, embedded systems, networking, backend, and frontend built from scratch with iterative improvements and real-world problem solving.
