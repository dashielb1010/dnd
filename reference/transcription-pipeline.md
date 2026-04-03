---
title: "Session Transcription Pipeline"
status: planned
last_updated: 2026-04-02
---

# Session Transcription Pipeline

How to get from a session audio recording to a structured, diarized transcript.

## Overview

Record sessions → transcribe + diarize locally with whisperX → post-process into session logs.

## Stack

### whisperX (primary tool)
- **What**: Open-source pipeline bundling Whisper + pyannote-audio + forced alignment
- **Why**: Diarized transcription with overlap-aware speaker segmentation, word-level timestamps, runs fully local
- **Repo**: https://github.com/m-bain/whisperX
- **Requirements**: Python 3.8+, PyTorch, ffmpeg
- **Model**: `large-v3` for best accuracy (runs well on Apple Silicon via CPU; faster with GPU)

### Components under the hood
| Component | Role |
|-----------|------|
| **faster-whisper** | Speech-to-text (CTranslate2 Whisper port — ~4x faster than vanilla) |
| **pyannote-audio 3.x** | Speaker diarization + overlap detection |
| **Forced alignment** | Word-level timestamps synced to diarization segments |

## Setup

```bash
# Create a dedicated environment
conda create -n whisperx python=3.10
conda activate whisperx

# Install whisperX
pip install whisperx

# ffmpeg (if not already installed)
brew install ffmpeg
```

**Note**: pyannote-audio requires accepting license terms on Hugging Face for the segmentation and diarization models. You'll need a HF token:
1. Create account at huggingface.co
2. Accept terms for `pyannote/segmentation-3.0` and `pyannote/speaker-diarization-3.1`
3. Generate an access token
4. Pass it via `--hf_token` or set `HF_TOKEN` env variable

## Usage

### Basic command
```bash
whisperx audio.wav --model large-v3 --diarize --hf_token $HF_TOKEN
```

### Recommended flags for D&D sessions
```bash
whisperx session-recording.wav \
  --model large-v3 \
  --diarize \
  --min_speakers 4 \        # set to expected player count (adjust per session)
  --max_speakers 8 \        # players + DM + buffer
  --language en \
  --output_format json \    # structured output for post-processing
  --output_dir ./raw/ \
  --hf_token $HF_TOKEN
```

### Output
whisperX produces per-file output with:
- **Word-level timestamps** — every word anchored to audio time
- **Speaker labels** — `SPEAKER_00`, `SPEAKER_01`, etc. (mapped to players manually or via a speaker map)
- **Segment boundaries** — including overlapping speech regions

Output formats: `json`, `srt`, `vtt`, `txt`, `tsv`

## Recommended Workflow

### 1. Record
- Central mic on the table, or ideally a multi-track setup (one channel per player)
- Save as WAV or high-quality MP3
- Name: `session-XX-raw.wav`
- Store in: `assets/recordings/` (add to .gitignore — these are large)

### 2. Transcribe
```bash
whisperx assets/recordings/session-XX-raw.wav \
  --model large-v3 \
  --diarize \
  --min_speakers 5 --max_speakers 8 \
  --language en \
  --output_format json \
  --output_dir campaign/sessions/transcripts/ \
  --hf_token $HF_TOKEN
```

### 3. Post-process
- Map speaker labels to player names (whisperX assigns generic `SPEAKER_XX` labels)
- Trim crosstalk / low-confidence segments if desired
- Format into readable transcript markdown
- Link or paste into the session log's `## Transcript` section

### 4. Summarize
- Use the raw transcript + your memory to fill in the session log template
- Claude or similar can help condense a raw transcript into the structured session format

## Directory Layout

```
assets/
  recordings/         # Raw audio files (gitignored — large)
    session-01-raw.wav
campaign/
  sessions/
    transcripts/      # whisperX output (json/srt)
      session-01-raw.json
    session-01.md     # Finished session log (links to or includes transcript)
```

## Overlap & Crosstalk

whisperX uses pyannote 3.x's overlap-aware segmentation, which can:
- Detect when multiple speakers are talking simultaneously
- Assign overlapping segments to multiple speakers
- Provide timestamps to jump back to audio for messy sections

**Realistic expectations**: It won't perfectly separate two people talking over each other into clean separate lines. It *will* flag overlaps, get the dominant speaker right most of the time, and give you timestamps to spot-check against the recording.

**Audio quality matters more than model choice** — a decent central mic (or better, per-player mics into a multi-track recorder) will improve results more than any software tuning.

## Future Improvements

- [ ] Script to automate the full record → transcribe → format pipeline
- [ ] Speaker mapping config (so `SPEAKER_00` = "DM", `SPEAKER_01` = "Jasper", etc.)
- [ ] Post-processing script to convert whisperX JSON → markdown transcript
- [ ] Evaluate Deepgram as a cloud fallback if local quality isn't sufficient for heavy-crosstalk sessions
