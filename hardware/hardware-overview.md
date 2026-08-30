# Hardware Overview: ESP 32 Wearable Unit

## 1. Core Component Selection
The SAGE wearable unit is built upon an ultra-small, low-power system-on-chip equipped with external visual and auditory sensors:

| Component | Technical Specification | Purpose |
| :--- | :--- | :--- |
| **Microcontroller** | Dual-core 240MHz processor, 8MB PSRAM, Wi-Fi/Bluetooth | Main control unit, buffers media, hosts AP server |
| **Image Sensor** | OV2640 VGA Camera (up to 15 FPS) | Captures visual frame buffers |
| **Microphone** | PDM digital microphone (I2S DMA) | Captures real-time audio |
| **Storage Card** | SD card interface (optional backup) | Persistent local storage backup |

---

## 2. Pin Configuration & Sensor Interfacing
The hardware firmware establishes separate DMA and bus configurations to communicate with the sensors:

*   **Camera Bus (SCCB/DVP):** Directly interfaces with the OV2640 image sensor using dedicated high-speed pins. Configured for VGA resolution to balance memory footprint and image details.
*   **Audio Bus (I2S):** Configured for PDM capturing mono audio at a sample rate of 16,000 Hz, mapping incoming audio signals directly into a pre-allocated PSRAM buffer.
*   **Wireless Module:** Configured as a local Access Point (AP) broadcasting a secure Wi-Fi SSID. A dedicated HTTP server is hosted on Core 1 to handle connection requests and stream local endpoints:
    - `/live_frame` - High-frequency live MJPEG viewfinder stream.
    - `/download_video` - High-speed binary transfer of AVI video buffers.
    - `/download_audio` - High-speed binary transfer of WAV audio buffers.
    - `/health` - System diagnostics and battery level.

---

## 3. Power Management & Heat Dissipation
Due to the thermal constraints of a wearable form-factor:
- **Processor Sleep States:** When no session is active, the camera is powered down and the microcontroller scales its frequency down to 80MHz.
- **Asynchronous Dual-Core Partitioning:** Running visual frame extraction on Core 0 and the HTTP server on Core 1 prevents core locks and reduces peak current spikes.
