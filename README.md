# The Broadcast Mic Post-Processing Blueprint

The secret to a professional, radio-ready broadcast or podcast sound isn't an expensive stack of software presets, multi-stage EQs, or aggressive digital processing. Relying on complex software enhancers—or stacking hardware presets like those found on the **Rødecaster Pro**, **Rødecaster Duo**, or external channel strips—is exactly what makes high-end microphones sound muddy, artificial, and cheap. 

The real blueprint is simple: **Record a clean, uncolored raw hardware signal with zero processing, and let a single-pass LUFS filter unlock the microphone's true value.** 

This repository provides a single-pass FFmpeg configuration using the `loudnorm` filter to instantly bring your raw microphone recordings to life, matching the commercial streaming standard of **-14 LUFS** and **-1 dBTP** while maintaining a completely **lossless, pure WAV workflow**.

---

## The Core Concept

* **No Hardware / Software Presets:** Avoid the built-in "Big Bottom," "Aural Exciter," or "Podcast Voice" presets on your audio interfaces and mixers. They color the sound unnaturally and destroy high-end clarity.
* **No Multi-Stage EQ:** Eliminates phase shifting, muddy mid-range build-up, and over-compressed frequency overlaps.
* **Pure Lossless Execution:** Keeps your files uncompressed throughout the normalization process. No transcoding degradation, no compressed artifacts. Just mathematical gain-staging directly into PCM audio.

---

## The Blueprint Commands

### 1. For Pure Audio-Only Tracks (Lossless Master)
Run this single-pass command on your raw, clean `.wav` microphone recording. It normalizes your levels while outputting a pristine, uncompressed 24-bit PCM WAV file:

```bash
ffmpeg -i "raw_mic_recording.wav" \
  -af "loudnorm=I=-14:TP=-1:LRA=11" \
  -c:a pcm_s24le \
  "broadcast_master.wav"
```
*(Note: If your original project was recorded in 16-bit, you can change `-c:a pcm_s24le` to `-c:a pcm_s16le`.)*

### 2. For Video Tracks (Video Podcasts / Streams)
If your raw microphone audio is already embedded inside a video file, this command processes the audio stream to **-14 LUFS** using high-quality uncompressed PCM or AAC depending on your output container, while safely copying the video stream (`-c:v copy`) to avoid losing quality or wasting re-encoding time:

```bash
ffmpeg -i "raw_podcast_video.mp4" \
  -af "loudnorm=I=-14:TP=-1:LRA=11" \
  -c:v copy -c:a aac -b:a 320k \
  "broadcast_video_master.mp4"
```

---

## Technical Specifications

| Filter Argument | Value | What it Actually Achieves |
| :--- | :--- | :--- |
| **`I=-14`** | `-14 LUFS` | **Integrated Loudness Target.** Brings the average volume of your voice exactly to the target distribution standard for all major streaming platforms. |
| **`TP=-1`** | `-1.0 dBTP` | **True Peak Ceiling.** Guarantees 1dB of safety headroom. This prevents digital clipping and distortion when streaming platforms transcode your file into lossy formats like AAC/MP3. |
| **`LRA=11`** | `11 LU` | **Loudness Range.** Optimizes your natural voice dynamics. It gently evens out the difference between quiet speaking and enthusiastic bursts without sounding squashed or lifeless. |

---

## License & Attribution

This blueprint configuration is licensed under the **BSD 3-Clause License**. 

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the copyright notice below, this list of conditions, and the following disclaimer.
2. Redistributions in binary form must reproduce the copyright notice below, this list of conditions, and the following disclaimer in the documentation and/or other materials provided with the distribution.
3. Neither the name of the copyright holder/inventor nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

**Copyright (c) 2026, [YOUR NAME / GITHUB USERNAME HERE] (Blueprint Creator & Inventor). All rights reserved.**
