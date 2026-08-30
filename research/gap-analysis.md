# Gap Analysis: Resource Constraints & Synchronization in Wearables

## 1. Introduction
While multimodal memory indexing is conceptually straightforward on high-performance desktop or mobile hardware, bringing continuous audio-visual capture to low-power, wearable microcontroller platforms introduces unique engineering bottlenecks.

---

## 2. Identified Technical Gaps

### Gap A: Hard Memory Exhaustion in Single-Buffer Architectures
On-chip memory (SRAM/PSRAM) on ultra-low-power microcontrollers (e.g., ESP32 architectures) is heavily restricted (typically 8MB or less of external PSRAM). 
*   **The Problem:** Storing a combined audio-video stream (e.g., standard MP4 or AVI) in a single unified buffer causes immediate heap fragmentation and stack overflows within 3–5 seconds of recording.
*   **The Impact:** Most existing commercial wearable solutions rely on the smartphone client doing the heavy lifting (streaming raw frames to the phone continuously). This quickly drains the phone's battery and causes packet drops over Wi-Fi.

### Gap B: Asynchronous Drift and Frame Dropping
When trying to capture high-speed camera frames (OV2640) and PDM audio samples concurrently on small chips, core contention arises.
*   **The Problem:** Camera frame capture takes variable time (50ms–200ms depending on ambient lighting and JPEG compression complexity). PDM microphone DMA transactions are time-critical and must run without interruption.
*   **The Impact:** A single-threaded loop will either drop audio samples (causing audio clicks) or drop camera frames (causing stuttering), leading to significant drift over a 15-second clip.

---

## 3. The SAGE Solution: Decoupled Dual-Buffer Architecture
SAGE addresses these gaps by implementing a **Decoupled Dual-Buffer Pipeline**:
1.  **Partitioned Buffering:** Audio and video are written to independent, non-contiguous blocks of PSRAM during capture.
2.  **Asynchronous FreeRTOS Execution:** Core 0 handles visual grabbing and audio collection on independent, prioritized tasks.
3.  **Downstream Synchronization:** Rather than muxing on the resource-constrained microcontroller, raw visual chunks (`video.avi`) and auditory buffers (`audio.wav`) are transferred independently and synchronized natively on the mobile client.
