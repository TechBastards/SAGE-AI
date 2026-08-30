# Media Synchronization Methodology

## 1. Frame Acquisition Rate
The visual channel captures frames from the OV2640 camera using DMA.
*   Due to JPEG compression latency and hardware bus speed limitations, the hardware records at a variable frame rate (~5 to 8 FPS).
*   During recording, the firmware records the exact frame count and the microsecond delay between frames, storing this information in the AVI header structure.

## 2. Audio Sample Acquisition
Concurrently, the digital microphone is read at 16,000 Hz (mono, 16-bit PCM).
*   The raw audio is written to a pre-allocated buffer in PSRAM.
*   Once recording stops, a standard 44-byte WAV header is prepended to compile a valid `WAV` file structure.

## 3. Client-Side Reconstruction & Playback
Upon successful download, the mobile application performs synchronization:
1.  **Frame Rate Calculation:** The app parses the AVI header to determine the exact number of frames and the total duration.
2.  **Display Time Decoupling:** The UI timeline separates visual playback refresh rate (fixed at 20 FPS) from the real-time timeline duration, dynamically scaling frame indexes to show exactly 15.0 seconds.
3.  **Proportional Seeking:** Media seeking works fractionally: when the user moves the playback slider, the app calculates the ratio `(slider_value / total_duration)` and seeks both the audio file and visual frame index to that exact percentage, maintaining perfect visual-auditory alignment.
