# LoRa Messenger — Offline WhatsApp

An offline peer-to-peer text and image messenger powered by **LoRa radio** and the **STM32 LoRa-E5 Mini** microcontroller, featuring a WhatsApp-style web interface using the **Web Serial API** (no internet required).

---

## Features

- **Offline P2P Messaging:** Send and receive text messages over LoRa radio (`868 MHz`, up to ~10 km line-of-sight range).
- **WhatsApp-Style Image Transfer:** Send and receive images (`JPG`, `PNG`, `BMP`, `GIF`, `WebP`) chunked into 152-byte base64 packets over LoRa, complete with:
  - Live upload progress and **Cancel** support (`IMG:CANCEL:`)
  - Missing-chunk recovery padding
  - Full-screen image lightbox viewer and one-click download
- **Delete for Everyone:** Remove sent messages across connected peers (`CMD:DEL:<msgId>`).
- **Persistent Chat History:** Saves up to 50 recent text/image messages in browser `localStorage`.
- **Serial Monitor & Telemetry:** Live TX/RX packet counters and raw serial debug console in the sidebar.
- **Private Group Channel:** Firmware filters incoming/outgoing LoRa packets by a shared `NETWORK_ID`.

---

## Project Structure

```text
├── index.html              # Web app UI (sidebar, chat area, image lightbox)
├── app.js                  # Web Serial API communication, LoRa text & chunked image protocol
├── style.css               # WhatsApp-inspired dark theme styling
├── firmware/
│   └── messenger.ino       # Arduino/RadioLib firmware for STM32 LoRa-E5 Mini
├── test_lora.bmp           # Sample small BMP image for LoRa image transfer testing
├── app/                    # Expo Router mobile app scaffold (tabs & modal)
├── components/             # Reusable React Native UI components
├── constants/              # App theme & color constants
├── hooks/                  # Color scheme & theme hooks
├── assets/images/          # App icons and splash assets
├── package.json            # Expo / React Native dependencies
└── app.json                # Expo configuration
```

---

## Hardware & Firmware Setup

1. **Hardware:** Two **STM32 LoRa-E5 Mini** boards (with 868 MHz antennas) connected via USB-to-Serial.
2. **Firmware (`firmware/messenger.ino`):**
   - Built with **RadioLib** (`STM32WLx`).
   - Radio settings: `868.0 MHz`, Bandwidth `125.0 kHz`, Spreading Factor `9`, Coding Rate `7`, Output Power `14 dBm`.
   - Serial pins: `PB6` (TX) / `PB7` (RX) at `115200` baud.
   - Update `NETWORK_ID` in `firmware/messenger.ino` if you want a custom private channel (must match on both boards).
3. Flash `firmware/messenger.ino` to both STM32 LoRa-E5 Mini boards.

---

## Running the Web App (LoRa Messenger)

> **Note:** Requires a browser that supports the **Web Serial API** (Google Chrome or Microsoft Edge).

1. Connect your flashed STM32 LoRa-E5 Mini board to your computer via USB.
2. Serve or open `index.html` in Chrome or Edge (e.g., using a local static server).
3. Set **Your Name**, ensure **Baud Rate** is `115200`, and click **Connect LoRa Board**.
4. Select the board's COM/Serial port. Once `READY` appears, start sending messages or images (try `test_lora.bmp` for a quick test).

---

## Serial & LoRa Protocol

| Packet Format | Description |
| :--- | :--- |
| `MSG:<id>:<sender>:<text>` | Text message (max 234 bytes total payload) |
| `CMD:DEL:<id>` | Delete message for everyone |
| `IMG:START:<file>:<chunks>:<id>` | Start chunked image transfer |
| `IMG:DATA:<file>:<idx>:<b64>` | Base64 image data chunk (152 chars) |
| `IMG:END:<file>` | End image transfer and assemble image |
| `IMG:CANCEL:<file>` | Cancel an in-progress image transfer |

---

## Expo Mobile App (Scaffold)

This repository also includes an Expo (`~54.0.33`) / React Native (`0.81.5`) starter project:

```bash
npm install
npx expo start
```
