# CLONE_VOICES.md - Sonic Identity Manual (Stark-Grade Cloning)

This document details the procedure for equipping the server with the Elite Gallery voices and OpenAI standards. For intellectual property and copyright reasons, third-party audio files are not provided with this software.

## 🛡️ Technical Sample Requirements (The "Raw Material")

For the XTTSv2 model to perform high-fidelity cloning, each reference file must comply with the following acoustic intelligence parameters:

1. **Duration:** Between 6 and 12 seconds. Less than 6 seconds reduces tonal depth; more than 12 increases initial loading latency without proportionally improving quality.
2. **Content:** Clear human voice. It must contain a complete sentence with natural intonation variations.
3. **Purity:** ZERO background noise. No music, sound effects, explosions, or echo. The presence of external frequencies will contaminate the voice output.
4. **Format:** .wav (PCM 16-bit or 32-bit float).
5. **Quality:** Minimum sampling rate of 22,050 Hz (44,100 Hz or higher recommended). Mono or Stereo (the engine will normalize it internally).

## 🚀 Installation Procedure

For automatic mappings to work, files must be placed in the assets directory defined in your environment variables (default: `/opt/ai/assets/voices/`).

### 📂 Directory Structure:

```text
/opt/ai/assets/voices/
├── standard/        <-- OpenAI compatibility voices
│   ├── alloy.wav
│   ├── echo.wav
│   ├── fable.wav
│   ├── onyx.wav
│   ├── nova.wav
│   └── shimmer.wav
└── elite/           <-- Elite Gallery of Artificial Intelligences
    ├── paul_bettany.wav    (Mapped to "jarvis")
    ├── kerry_condon.wav    (Mapped to "friday")
    ├── hal9000.wav         (Mapped to "hal")
    ├── scarlett_johansson.wav
    ├── cortana.wav
    ├── glados.wav
    ├── tars.wav
    ├── kitt.wav
    └── rachel.wav
```

## 🛠️ Advanced Workflow: From Source to Sample

To obtain a professional-grade sample from any online source (such as YouTube) or a local file, you can follow this end-to-end workflow. This process involves downloading, precise extraction, and acoustic optimization.

### 1. Download & Initial Extraction (YouTube)
Use `yt-dlp` to extract the highest quality audio directly. For iconic voices like HAL 9000, seek high-quality compilations:

```bash
# Install yt-dlp if not present
pip install yt-dlp

# Download the source (Example: HAL 9000 Full Compilation)
yt-dlp -x --audio-format wav -o "raw_source.wav" "https://www.youtube.com/watch?v=9wrjl-H4Hs8"
```

### 2. Identify and Isolate the Target Segment
Find a segment (6-12s) where the character speaks clearly.

```bash
# Isolate a clear part of the clip
ffmpeg -i raw_source.wav -ss 00:00:48 -t 10 -ac 1 -ar 22050 clear_sample.wav
```

### 3. Professional Mastering & Denoising (Advanced)
If the source has persistent background hiss (common in older films), use `sox` to create a noise profile and clean the sample:

```bash
# 1. Isolate a short segment of pure background noise (no voice)
sox clear_sample.wav noise_only.wav trim 0 0.5

# 2. Generate the noise profile
sox noise_only.wav -n noiseprof voice_noise.prof

# 3. Apply noise reduction to the main sample
sox clear_sample.wav mastered_voice.wav noisered voice_noise.prof 0.21
```

### 4. Alternative: One-Step Mastering with FFmpeg
If the source is relatively clean, this unified command handles isolation and normalization:

```bash
ffmpeg -i raw_source.wav \
  -ss 00:01:25 -t 10 \
  -acodec pcm_s16le -ar 44100 -ac 1 \
  -af "highpass=f=200, lowpass=f=3000, loudnorm=I=-16:TP=-1.5:LRA=11" \
  /opt/ai/assets/voices/elite/target_voice.wav
```

### ⚙️ Command Breakdown:
* `-ss 00:01:25`: Starts extraction at 1 minute and 25 seconds.
* `-t 10`: Extracts exactly 10 seconds.
* `-acodec pcm_s16le`: Encodes in 16-bit PCM (WAV standard).
* `-ar 44100 -ac 1`: Sets 44.1kHz sample rate and forces Mono (cleaner for cloning).
* `-af "..."`: Audio filters chain:
    * `highpass=f=200`: Removes low-end rumble and power line hum.
    * `lowpass=f=3000`: Removes high-frequency hiss (expand to 5000 for modern high-bitrate sources).
    * `loudnorm`: Normalizes the voice to EBU R128 standards for a punchy, consistent presence.

## 🔍 Tips for Sophisticated Cloning

* **Normalization:** Use tools like Audacity or FFmpeg to normalize the sample volume to -3dB.
* **Cleaning:** Apply a noise reduction filter if the original clip comes from an analog source or an old film (such as HAL 9000).
* **Fidelity:** The quality of the clone is directly proportional to the quality of the sample. If the "Raw Material" is poor, the server's response will lack soul.

"Perfection is not a detail, but details make perfection."
-- J.A.R.V.I.S. A.I.
