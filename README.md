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
## The Non-Negotiable Physical Rule: 100% On-Axis Delivery

The single-pass LUFS filter chain is not magic; it is a mathematical distribution engine. For this blueprint to successfully pull the treble out of the mud, **you must capture the treble at the acoustic capsule first.** 

### 1. Off-Axis Audio Kills Treble Experimentally
Broadcast dynamic microphones (like the Røde Procaster) are highly directional pressure-gradient transducers. High frequencies are exceptionally short and directional sound waves:
* **The Physics:** If you speak off-axis (tilted away or talking into the side of the microphone barrel), the fast, crisp high-end transients completely miss the capsule's primary pick-up zone.
* **The Consequence:** Off-axis placement introduces a permanent acoustic roll-off. It strips away the clarity, definition, and presence of your voice before the signal ever hits your audio interface. 

### 2. Software Cannot Rebuild What Was Never Captured
If you feed a muddy, off-axis recording into this filter chain, it will fail. Because the high frequencies are physically missing from the raw waveform, the `loudnorm` filter will calculate its distribution targets based entirely on your heavy low-mid mud. The result is a boomy, compressed, and suffocating output.

### 3. The Flawless Setup Blueprint
To achieve 100% true broadcast performance, your physical delivery must be entirely disciplined:
* **100% On-Axis Angle:** Point the top dome grille of the Procaster directly at your lips. Deliver your speech straight down the barrel of the microphone.
* **Mechanical Diffusion Over Foam Blankets:** If plosives ("P" and "B" pops) are an issue, **do not** stack heavy foam windscreens or thick fabric pop filters over the mic—they act as high-cut filters and replicate the muffled factory characteristics. Instead, strip away internal foam restrictions and place a micro-thin, low-density open-cell acoustic foam slice directly over the top of the capsule frame as a mechanical air diffuser.

By ensuring a **100% on-axis straight delivery**, your raw input audio retains its native transient detail. This provides the LUFS engine with a pristine, full-spectrum waveform, allowing it to balance the track beautifully without needing a single digital equalizer preset.

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
## The Real-World Test: Procaster vs. NT1-A (Podcast Usecase)

When you test this raw-to-LUFS blueprint in a real podcast-broadcast setup, the results are shocking:

* **The Procaster Without This Chain:** It sounds absolutely terrible. It is muddy, the treble is completely buried under a heavy wall of mids, and it honestly sounds worse than a $5 karaoke microphone. 
* **The Procaster After This Chain:** Running this single LUFS filter pulls the treble right out of the mud, completely opening up the sound. It suddenly transforms into a rich, premium broadcast masterpiece.
* **The NT1-A Comparison:** The Røde NT1-A is an excellent, ultra-clean $150 studio condenser. But once you apply this exact LUFS filter to both raw tracks, the NT1-A sounds incredibly dull and boring. The normalized Procaster completely destroys it for broadcast use.
---
## Prior Art & Attribution: The Scope of This Invention

To understand the scope of this blueprint, a clear distinction must be made between the underlying open-source processing tools and the original philosophy introduced by this repository:

### 1. Underlying Technology (Prior Art)
* **The `loudnorm` Filter:** The underlying mathematical loudness normalization filter belongs to the open-source FFmpeg project (based on the international EBU R128 loudness standard introduced to the codebase in 2016).
* **The Targets:** Compressing or normalizing audio files to a baseline of **-14 LUFS** and **-1 dBTP** is the standard, documented technical specification required by distribution platforms like YouTube and Spotify.

### 2. The Original Invention (The Philosophy)
While the code string itself leverages existing engineering libraries, **the conceptual framework and application strategy introduced here are an industry first.**

Traditionally, the broadcast and podcast community treats muddy, mid-heavy dynamic microphone recordings as a problem that must be corrected using hardware presets (e.g., Rødecaster processing) or complex, multi-stage software EQ plugins. 

**This blueprint introduces an entirely new post-processing paradigm:**
* **Perceptual Treble Extraction:** It identifies that the buried treble definition in high-end dynamic capsules like the Røde Procaster is not a failure of frequency response, but a symptom of low perceived loudness.
* **The Anti-Preset Manifesto:** It proves that a single-pass, uncompressed WAV-to-WAV loudness normalization is all that is required to instantly pull those hidden high frequencies out of the mid-range mud. 

By eliminating the need for expensive hardware middlemen and multi-stage plugins, this project serves as an original, iconoclasonic architectural guide—repurposing a data-engineering automation tool into a free, premium audio mastering solution for content creators.

---


## License & Attribution

This blueprint configuration is licensed under the **BSD 3-Clause License**. 

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the copyright notice below, this list of conditions, and the following disclaimer.
2. Redistributions in binary form must reproduce the copyright notice below, this list of conditions, and the following disclaimer in the documentation and/or other materials provided with the distribution.
3. Neither the name of the copyright holder/inventor nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

**Copyright (c) 2026, [Arto Mäenpää / arto-maenpaa ] (Blueprint Creator & Inventor). All rights reserved.**
