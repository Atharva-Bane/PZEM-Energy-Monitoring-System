# Setup Guide

This guide explains how to build and run the PZEM Energy Monitoring System from scratch.

---

## 1. Hardware Requirements

* ESP32 (I chose 30pin esp32)
* PZEM-004T Energy Meter Module
* Relay Module
* Electrical Load (i chose a bulb of known power)
* Connecting wires

---

## 2. Hardware Connections

### PZEM → ESP32

* TX → RX
* RX → TX
* VCC → 3.3V
* GND → GND

### Relay → ESP32

* IN → GPIO (any digital pin - I chose D5 for easier connection)
* VCC → 5V
* GND → GND

---

## Safety Note

Ensure proper insulation and safe handling of high voltage connections.

Refer the circuit diagram for hardware connection from media section.

I chose KiCad to design the circuit diagram.

---

## 3. Software Setup

### Install Required Software

* Arduino IDE (for ESP32 programming)
* Mosquitto MQTT Broker
* Node.js
* React UI

---

## 4. ESP32 Setup

1. Paste this Arduino code into your Arduino IDE

```
#include <WiFi.h>
#include <PubSubClient.h>
#include <PZEM004Tv30.h>

// WiFi Credentials
const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";

// MQTT Broker Settings
const char* mqtt_server = "YOUR_LAPTOP_IP";   // Example: 192.168.1.33
const int mqtt_port = 1883;

// MQTT Topics
const char* dataTopic = "esp32/pzem/data";
const char* relayTopic = "esp32/pzem/relay";

// Relay Pin
#define RELAY_PIN 5

// PZEM Pins
#define RXD2 16
#define TXD2 17

// ESP32 Serial2 for PZEM
HardwareSerial PZEMSerial(2);
PZEM004Tv30 pzem(PZEMSerial, RXD2, TXD2);

WiFiClient espClient;
PubSubClient client(espClient);

String relayState = "OFF";
unsigned long lastPublishTime = 0;

// Connect to WiFi
void setup_wifi() {
  delay(10);
  Serial.println();
  Serial.print("Connecting to WiFi: ");
  Serial.println(ssid);

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println();
  Serial.println("WiFi Connected");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());
}

// MQTT Callback
void callback(char* topic, byte* payload, unsigned int length) {
  String message = "";

  for (int i = 0; i < length; i++) {
    message += (char)payload[i];
  }

  Serial.print("Message received on topic: ");
  Serial.println(topic);
  Serial.print("Message: ");
  Serial.println(message);

  if (String(topic) == relayTopic) {

    // Active LOW Relay Logic
    if (message == "ON") {
      digitalWrite(RELAY_PIN, LOW);
      relayState = "ON";
      Serial.println("Relay Turned ON");
    }
    else if (message == "OFF") {
      digitalWrite(RELAY_PIN, HIGH);
      relayState = "OFF";
      Serial.println("Relay Turned OFF");
    }
  }
}

// Reconnect MQTT
void reconnect() {
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");

    String clientId = "ESP32Client-";
    clientId += String(random(0xffff), HEX);

    if (client.connect(clientId.c_str())) {
      Serial.println("Connected to MQTT Broker");

      client.subscribe(relayTopic);
      Serial.print("Subscribed to: ");
      Serial.println(relayTopic);
    }
    else {
      Serial.print("Failed, rc=");
      Serial.print(client.state());
      Serial.println(" Trying again in 5 seconds...");
      delay(5000);
    }
  }
}

void setup() {
  Serial.begin(115200);

  pinMode(RELAY_PIN, OUTPUT);

  // Relay OFF at startup (Active LOW relay)
  digitalWrite(RELAY_PIN, HIGH);

  PZEMSerial.begin(9600, SERIAL_8N1, RXD2, TXD2);

  setup_wifi();

  client.setServer(mqtt_server, mqtt_port);
  client.setCallback(callback);
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }

  client.loop();

  // Publish every 2 seconds
  if (millis() - lastPublishTime > 2000) {
    lastPublishTime = millis();

    float voltage = pzem.voltage();
    float current = pzem.current();
    float power = pzem.power();
    float energy = pzem.energy();
    float frequency = pzem.frequency();
    float pf = pzem.pf();

    // Replace invalid values with 0
    if (isnan(voltage)) voltage = 0;
    if (isnan(current)) current = 0;
    if (isnan(power)) power = 0;
    if (isnan(energy)) energy = 0;
    if (isnan(frequency)) frequency = 0;
    if (isnan(pf)) pf = 0;

    // If relay is OFF, force load values to 0
    if (relayState == "OFF") {
      current = 0;
      power = 0;
      pf = 0;
    }

    // Create JSON payload
    String payload = "{";
    payload += "\"voltage\":" + String(voltage, 2) + ",";
    payload += "\"current\":" + String(current, 2) + ",";
    payload += "\"power\":" + String(power, 2) + ",";
    payload += "\"energy\":" + String(energy, 3) + ",";
    payload += "\"frequency\":" + String(frequency, 2) + ",";
    payload += "\"pf\":" + String(pf, 2) + ",";
    payload += "\"relay\":\"" + relayState + "\"";
    payload += "}";

    // Publish to MQTT
    client.publish(dataTopic, payload.c_str());

    // Print on Serial Monitor
    Serial.println("================================");
    Serial.println("Published MQTT Data:");
    Serial.println(payload);
    Serial.println("================================");
  }
}
```

Don't forget to download the libraries.


2. Update:

   * WiFi SSID
   * WiFi Password
   * MQTT Broker IP (just type ipconfig in command prompt and enter the IPV4 address, if you have installed mosquitto on the same device)

3. Upload code to ESP32

---

## 5. Backend (Node.js) & Frontend (React) Setup

1. Install Node.js

   
Go to Node.js website


Download the LTS version


Install it with default settings


After install, open Command Prompt and type:

```
node -v
```
```
npm -v
```

You should see version numbers.

2. Create a project folder

   
For example:

```
mkdir nexgrid-dashboard
```
```
cd nexgrid-dashboard
```

(Remember to run command prompt as administrator before this)

3. Create the React frontend project

```
npm create vite@latest frontend -- --template react
```

Then enter:

```
cd frontend
```
```
npm install
```
```
npm install tailwindcss framer-motion recharts socket.io-client lucide-react
```

4. Start the React project once to test

```
npm run dev
```

You will see a localhost link like:

http://localhost:5173

Open it in browser. You should see the default Vite page.

5. Stop the frontend server


Press:
Ctrl + C

6. Go back to your main project folder

```
cd ..
```

7. Create the backend folder

```
mkdir backend
```
```
cd backend
```
```
npm init -y
```
```
npm install express socket.io mqtt cors
```

8. Create backend file

Inside backend folder, create file named:

```
server.js
```

Paste this code:

```
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');
const mqtt = require('mqtt');

const app = express();
const server = http.createServer(app);

const io = new Server(server, {
  cors: {
    origin: '*',
  },
});

const mqttClient = mqtt.connect('mqtt://localhost:1883');

mqttClient.on('connect', () => {
  console.log('Connected to MQTT broker');
  mqttClient.subscribe('esp32/pzem/data');
});

mqttClient.on('message', (topic, message) => {
  try {
    const data = JSON.parse(message.toString());
    console.log('Received:', data);

    io.emit('sensorData', data);
  } catch (err) {
    console.log('Invalid JSON received');
  }
});

io.on('connection', (socket) => {
  console.log('React client connected');

  socket.on('relayCommand', (state) => {
    console.log('Relay command:', state);

    mqttClient.publish('esp32/pzem/relay', state);
  });
});

server.listen(3001, () => {
  console.log('Backend running on http://localhost:3001');
});
```

9. Test backend once

```
node server.js
```

If successful, you should see:

Backend running on http://localhost:3001

10. Install and start Mosquitto


If not installed already:


Download Mosquitto


Install it


During install, tick:
* Install service
* Install broker
* Install client utilities

Then open Command Prompt and run:

```
mosquitto -v
```

If command not found, go to Mosquitto installation folder and run from there.

Usually:

cd "C:\Program Files\mosquitto"
mosquitto -v

11. Keep 3 terminals open at same time

Terminal 1:

```
cd "C:\Program Files\mosquitto"
```
```
mosquitto -v
```

Terminal 2:

```
cd path\to\nexgrid-dashboard\backend
```
```
node server.js
```

Terminal 3:

```
cd path\to\nexgrid-dashboard\frontend
```
```
npm run dev
```

12. Replace frontend code


Open:
`
```
frontend/src/App.jsx
```

Delete everything inside and replace with:

```
import { useEffect, useState } from 'react';
import { io } from 'socket.io-client';

const socket = io('http://localhost:3001');

function App() {
  const [metrics, setMetrics] = useState({
    voltage: 0,
    current: 0,
    power: 0,
    energy: 0,
    frequency: 0,
    pf: 0,
  });

  useEffect(() => {
    socket.on('sensorData', (data) => {
      setMetrics(data);
    });

    return () => {
      socket.off('sensorData');
    };
  }, []);

  const relayOn = () => {
    socket.emit('relayCommand', 'ON');
  };

  const relayOff = () => {
    socket.emit('relayCommand', 'OFF');
  };

  return (
    <div style={{ padding: '30px', fontFamily: 'Arial' }}>
      <h1>NexGrid Dashboard</h1>

      <h2>Voltage: {metrics.voltage} V</h2>
      <h2>Current: {metrics.current} A</h2>
      <h2>Power: {metrics.power} W</h2>
      <h2>Energy: {metrics.energy} kWh</h2>
      <h2>Frequency: {metrics.frequency} Hz</h2>
      <h2>Power Factor: {metrics.pf}</h2>

      <button onClick={relayOn}>Relay ON</button>
      <button onClick={relayOff} style={{ marginLeft: '10px' }}>
        Relay OFF
      </button>
    </div>
  );
}

export default App;
```

13. Save file and refresh browser


Your website should now show live values.

---


## 8. System Execution Flow

1. Start MQTT broker

```
net start mosquitto
```

2. Run Node.js backend

```
cd C:\your_path\backend
```
```
node server.js
```

3. Start React frontend

```
cd C:\your_path\frontend
```
```
npm run dev
```

4. Open brower

http://localhost:5173 

(It can be different for you)

5. Power ESP32 and flash the code

---

## Expected Output

* Real-time data displayed on dashboard
* Toggle button controls relay
* CSV file available for download

---

## Troubleshooting

### No MQTT Connection

* Check broker IP
* Check port (1883)
* Ensure same network

---

### No Data on Dashboard

* Verify Node.js is running
* Check topic names

---

### Relay Not Working

* Check GPIO pin
* Verify logic (active-low/active-high)

---

## Final Note

This system integrates hardware, networking, and web technologies. Ensure each module is tested individually before full system integration.
