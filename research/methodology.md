# Methodology: Asynchronous Dual-Channel Capture and Semantic Recall

## 1. Asynchronous Capture Cycle
SAGE operates on an automated, state-machine-driven loop designed to minimize power consumption while capturing continuous media:

```
[Idle viewfinder] 
     ⬇️ (Start session)
[Recording State] ── Core 0 captures Video (PSRAM) & Audio (DMA) for 15 seconds
     ⬇️ (Stop command)
[Clip Ready] ────── Live-stream pauses; client connects to AP
     ⬇️ (Download endpoint)
[Transferring] ──── Client pulls video.avi and audio.wav in parallel
     ⬇️ (Mux & Transcode)
[Saving & Cache] ── Client saves to session folder, runs local FFmpeg muxing
     ⬇️ (30s Decision Window)
[Waiting Loop] ──── User chooses to terminate or automatically start next clip
```

---

## 2. Audio-Visual Synchronization
To maintain synchronization without overloading the wearable processor:
*   **Video Packaging:** Frame chunks are written in real-time to a pre-allocated AVI file structure in PSRAM. Microseconds-per-frame parameters are embedded directly into the header.
*   **Audio Packaging:** The 16kHz mono PDM stream is buffered and packed with standard RIFF headers into a `WAV` file.
*   **Downstream Muxing:** The client reads the metadata, calculates the achieved FPS from the AVI headers, and launches an asynchronous background transcode to generate an MP4 file with aligned audio.

---

## 3. Cognitive Processing & Semantic Mapping
1.  **AI Multi-models Pipeline:** The mobile client submits the completed clip to the AI processing layer.
2.  **Multimodal Description:** The model generates:
    -   A descriptive Title (used for folder indexing).
    -   A concise summary of active events.
    -   A highly dense text prompt to serve as a visual recreation descriptor.
3.  **Local Memory Store:** Summaries are written to a localized chronological timeline database.
4.  **Google Drive Archive:** Files are queued for background upload to a nested cloud directory structure:
    `SAGE_ROOT/User_Name/Session_ID/Clip_ID/`
    - `video.avi`
    - `audio.wav`
    - `prompt.txt`
    - `metadata.json`

---

## 4. Semantic Query & Retrieval
SAGE implements a Retrieval-Augmented Generation (RAG) loop for conversational query search:
1.  **Search Indexing:** The mobile client parses user text or voice questions.
2.  **Matching:** Matches queries against local memory logs.
3.  **Prompt Engineering:** Packages matched memory metadata as context for the AI Multi-model chat.
4.  **Interactive Response:** Displays the conversational answer with direct links to the relevant media file stored on Google Drive.
