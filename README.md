# Linux Server MQTT Monitor for Home Assistant

A lightweight Python script that monitors the health of a Linux server (CPU, RAM, temperatures, Docker containers, services) and sends the data to an MQTT broker.
Built for **Home Assistant**, using **MQTT Auto-Discovery**, so all sensors appear automatically with zero YAML configuration.

## 🚀 Features
- **MQTT Auto-Discovery**
- CPU usage (total + per core)
- RAM usage
- CPU temperature
- Load averages
- System uptime
- Docker container monitoring
- Systemd service monitoring (default: Jellyfin)

## 📋 Prerequisites
- Linux server (Ubuntu/Debian/RPi OS)
- Python 3.6+
- MQTT broker (e.g., Mosquitto)
- Home Assistant with MQTT enabled

## 🛠️ Installation
### 1. Place the Script
```
/opt/server-monitor/
```

### 2. Install Dependencies
```
sudo apt update
sudo apt install python3-pip
pip3 install psutil paho-mqtt
```
Alternative:
```
sudo apt install python3-psutil python3-paho-mqtt
```

### 3. Docker Permissions
```
sudo usermod -aG docker $USER
```
Log out and in.

## ⚙️ Configuration
Edit your script:
```
BROKER = "192.168.1.X"
PORT = 1883
USERNAME = "your_mqtt_user"
PASSWORD = "your_mqtt_password"
TOPIC = "home/server/stats"
DEVICE_NAME = "main_server"

CONTAINERS = ["plex", "homeassistant", "zigbee2mqtt"]
```

## 🏃‍♂️ Running
### Manual:
```
python3 monitor.py
```

### Systemd Service:
Create:
```
sudo nano /etc/systemd/system/mqtt-monitor.service
```
Content:
```
[Unit]
Description=Linux Server MQTT Monitor
After=network.target docker.service mosquitto.service

[Service]
User=yourusername
Group=docker
ExecStart=/usr/bin/python3 /opt/server-monitor/monitor.py
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```
Enable:
```
sudo systemctl daemon-reload
sudo systemctl enable mqtt-monitor.service
sudo systemctl start mqtt-monitor.service
```
Check:
```
sudo systemctl status mqtt-monitor.service
```

## 🏠 Home Assistant
Entities appear automatically under the MQTT integration.
Examples:
- sensor.main_server_cpu_usage
- sensor.main_server_ram_usage
- sensor.main_server_cpu_temp
- sensor.main_server_plex_container

## 🔧 Customization
Change monitored service:
```
["systemctl", "is-active", "jellyfin"]
```
Replace `jellyfin` with your service.

## 📝 Troubleshooting
- No temperature → VM or kernel limitation.
- Docker permission denied → ensure user in docker group.
- MQTT refused → check credentials + broker IP.

## ✔️ Finished!
Your server now automatically exposes all metrics to Home Assistant via MQTT.

