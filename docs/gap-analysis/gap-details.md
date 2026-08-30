# Deep Gap Analysis: Wearable Systems Constraints

## 1. Single-Core Processing Bottleneck
Most affordable IoT development boards run visual processing and networking on a single core:
*   **Contention:** Camera driver interrupts disrupt TCP network task scheduling. This leads to dropped frames during streaming or packet timeout failures.
*   **SAGE Solution:** SAGE uses dual-core partitioning. Core 0 is dedicated to camera capture, while Core 1 runs the HTTP server. This prevents core locks and ensures stable streaming.

---

## 2. Combined Muxing Overhead
Microcontrollers do not have the processing power to mux MP4 container files:
*   **Overhead:** Performing H.264 compression or AAC encoding requires heavy floating-point math and dedicated codecs.
*   **SAGE Solution:** SAGE uses a decoupled dual-buffer capture pipeline, deferring the processing overhead. Muxing and transcoding are done on the mobile client using FFmpeg.
