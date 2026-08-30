# System Architecture Details

## 1. Embedded Wearable Tier
*   **Device Operating System:** FreeRTOS environment.
*   **Dual-Core Execution Model:**
    - **Core 0 (Capture Thread):** Continuous acquisition of OV2640 visual frame blocks and digital PDM audio streams. Executes DMA transfers for audio directly into partitioned PSRAM memory blocks.
    - **Core 1 (Server Thread):** Hosts an asynchronous WebServer. Listens for REST commands from the client (start/stop) and handles high-throughput binary chunk streaming for files over HTTP.

---

## 2. Cross-Platform Mobile Tier
*   **Application Framework:** Flutter.
*   **State Management:** Provider pattern (centralizes recording and session lifecycle).
*   **Muxing Subsystem:** Utilizes compiled FFmpeg bindings to merge separate audio and video buffers into standardized MP4 containers.
*   **Local Caching:** Encrypted key-value preferences and JSON file storage in application directories.

---

## 3. Cognitive AI & Search Tier
*   **AI Engine:** Multi-models layer.
*   **Functional Tasks:**
    - **Event Synthesis:** Summarizes activities based on joint audio-video content.
    - **Semantic Representation:** Generates text prompts and titles.
    - **Vector Chat Engine:** Resolves queries by searching semantic timelines and context logs.

---

## 4. Storage & Cloud Synchronization Tier
*   **Local Storage:** High-speed folder hierarchy categorized by Date and Session timestamp.
*   **Cloud Hosting:** Transactional file queue targeting private Google Drive folders. Uses OAuth 2.0 authentication tokens. Includes verification mechanisms (MD5 checksum matches) to ensure zero data corruption during wireless uploads.
