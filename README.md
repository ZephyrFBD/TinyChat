# TinyChat

TinyChat is a **miniature standalone chat device** based on the ESP32-S3.  
It enables cross-LAN and even cross-Internet messaging without requiring a smartphone.  
The device fits into a compact **5 × 5 × 2 cm** enclosure, powered by a single Li-ion cell,  
and integrates an OLED screen, a 5-way navigation key, a buzzer, and a vibration motor.  
A magnetic connector is used for charging and peripheral expansion.  

Communication is handled through a self-hosted server with **end-to-end encryption** and **TLS secure transport**,  
ensuring privacy of all messages.

---

## ✨ Features

- **Standalone chat** – communicates over Wi-Fi directly with your own server.  
- **End-to-end encryption** – AES or similar encryption, server only relays encrypted content.  
- **Multi-notification system** – OLED display, vibration, and buzzer alerts.  
- **Compact design** – all hardware within 5×5×2 cm, magnetic expansion port (SPI/I2C/Power).  
- **Low power** – Modem Sleep + deep sleep modes for extended battery life.  

---

## 🛠️ Hardware Design

### Core
- **ESP32-S3** – dual-core LX7, integrated Wi-Fi (2.4GHz), BLE, hardware crypto, low-power sleep.

### Input / Output
- **5-way navigation key** – menu navigation, selection, send/confirm.  
- **OLED display** – 0.96”–1.3” I2C screen for text, icons, status, and chat messages.  
- **Buzzer & vibration motor** – message alerts and haptic feedback.  

### Power Management
- **Battery**: 3.7V Li-ion cell, ~500mAh.  
- **Charging**: TP4056 or MPS IC with magnetic connector input.  
- **Protection**: DW01/FS8205A over-charge/over-discharge IC.  
- **Low-power modes**: OLED off, Wi-Fi Modem Sleep, dynamic CPU scaling.  

### Magnetic Connector
- **Functions**: Charging + expansion (SPI, I2C, VBAT, GND).  
- **Connector**: Gold-plated pogo pins for durability and low resistance.  
- **Magnetic alignment**: Prevents misconnection and ensures auto-alignment.  

### Example Pin Mapping

| Function          | ESP32-S3 GPIO | Notes                 |
|-------------------|---------------|-----------------------|
| OLED SDA          | GPIO8         | I2C data              |
| OLED SCL          | GPIO9         | I2C clock             |
| Nav Key Up        | GPIO2         | Internal pull-up      |
| Nav Key Down      | GPIO3         | Internal pull-up      |
| Nav Key Left      | GPIO4         | Internal pull-up      |
| Nav Key Right     | GPIO5         | Internal pull-up      |
| Nav Key Center    | GPIO6         | External interrupt    |
| Buzzer PWM        | GPIO10        | Tone control          |
| Vibration Motor   | GPIO11        | MOSFET driver         |
| SPI MOSI          | GPIO21        | Expansion             |
| SPI MISO          | GPIO20        | Expansion             |
| SPI SCLK          | GPIO19        | Expansion             |
| SPI CS            | GPIO18        | External chip select  |
| VBAT              | —             | Power pin (magnetic)  |
| GND               | —             | Ground pin (magnetic) |

---

## 📑 Firmware Architecture

### Modules
- **Wi-Fi / Network Manager** – STA/AP modes, WebSocket handling, auto reconnect.  
- **Security / Crypto** – TLS + AES end-to-end encryption, key management.  
- **Button Input** – 5-way key handling (polling/interrupt).  
- **OLED UI** – chat text, icons, status, scrolling messages.  
- **Buzzer & Vibration** – notifications + UI feedback.  
- **Power Manager** – sleep strategies, low battery protection.  
- **Message Handler** – buffer, send/receive, encrypt/decrypt, UI refresh.  

### Workflow
1. **Boot Init** – load device ID/keys, init peripherals (OLED, keys, Wi-Fi, buzzer/vibrator).  
2. **Wi-Fi Connect** – connect to saved network, fallback to AP config mode.  
3. **Server Auth** – WebSocket over TLS, device verification.  
4. **Messaging** – AES encrypt before send, decrypt on receive, display + notify.  
5. **UI Feedback** – key navigation updates OLED, notifications triggered if needed.  
6. **Low Power** – auto sleep, wake on key or incoming message.  

---

## 🌐 Server Design

- **Stack**: Node.js (`ws`) or Python (Tornado/Django Channels).  
- **Protocol**: WebSocket over TLS (`wss://`) – persistent, bidirectional messaging.  
- **Core Functions**:
  - Device registration & authentication  
  - Online presence tracking  
  - Encrypted message forwarding (no storage of plaintext)  
  - End-to-end encryption support  

### Example Message Format (JSON)

```json
{
  "from": "deviceID1",
  "to": "deviceID2",
  "ts": 1690000000,
  "msg": "base64(AES_ciphertext)",
  "meta": { "type": "text", "seq": 123 }
}
```

⸻

📂 Project Structure

/TinyChat
  /hardware
    schematics/
    PCB/
    BOM.xlsx
  /firmware
    src/
      main.cpp
      wifi_manager.cpp
      crypto.cpp
      ui.cpp
      button.cpp
      power.cpp
    lib/
    platformio.ini   # or Arduino .ino
  /server
    server.js or main.py
    package.json or requirements.txt
    README.md
  /docs
    README.md
    protocol.md


⸻

🚀 Development Priorities
	1.	Hardware validation (ESP32-S3 dev board + OLED, buttons, buzzer, vibrator).
	2.	Wi-Fi + server communication (WebSocket wss:// send/receive).
	3.	UI & interactions (OLED + navigation key).
	4.	Encryption (AES integration + E2E tests).
	5.	Power management (sleep/wake, battery endurance).
	6.	Magnetic connector design (pogo pin expansion).
	7.	PCB & enclosure (fit within 5×5×2 cm).

⸻

⚠️ Notes
	•	Single key input limits text entry → use predefined short messages or emoji table.
	•	Keep ESP32-S3 antenna area clear for stable Wi-Fi.
	•	Ensure magnetic connector reliability (low resistance, minimal interference).
	•	Always include battery protection ICs for safety.

⸻

📚 References
	•	ESP32-S3 Technical Reference Manual
	•	OLED SSD1306 drivers & libraries
	•	Pogo Pin connector design guides
	•	AES + TLS in embedded systems
	•	DRV2605L vibration driver datasheet