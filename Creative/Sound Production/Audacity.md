[⬅ Back to Sound Production Fundamentals](./[0]-Introduction-to-SoundProduction.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/audacity.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Audacity</h1>
</div>

Audacity is a free, open-source audio editor and recorder for Windows, macOS, and Linux. It's a popular choice for podcasts, voice-overs, interviews, and quick audio cleanup.

Website: [https://www.audacityteam.org/download/](https://www.audacityteam.org/download/)

---

## What Is Audacity?

Audacity displays audio as a waveform that you can cut, move, and process directly. It supports multi-track editing, so you can layer voice, music, and sound effects in a single project. It is lightweight, easy to learn, and a great first audio editor, though it is not a full music-production studio.

It is commonly used for:

- Recording and editing podcasts
- Cleaning up voice-overs and interviews
- Removing background noise
- Converting and exporting audio between formats
- Simple multi-track mixing

---

## Getting Started

1. Download Audacity from the official website and install it.
2. Open **Edit → Preferences → Devices** and choose your microphone as the recording device.
3. Set the project sample rate to 44100 Hz (music) or 48000 Hz (video) in the bottom-left corner.
4. Do a short test recording and check that the waveform peaks stay below clipping (never touching the top and bottom edges).

---

## The Interface

| Area | Purpose |
|---|---|
| **Transport toolbar** | Play, record, pause, stop, and loop |
| **Tools toolbar** | Selection, envelope, draw, and zoom tools |
| **Track area** (center) | Waveforms of each audio track |
| **Track controls** (left of each track) | Mute, solo, gain, and pan |
| **Level meters** | Show input and playback volume |
| **Effect / Analyze / Tools menus** | Processing, analysis, and automation |

---

## Key Concepts

- **Tracks** – each track holds a separate piece of audio, such as a voice or background music.
- **Selection and Trim** – select part of a waveform to cut, copy, silence, or delete it.
- **Effects** – built-in tools such as Noise Reduction, Normalize, Compressor, Equalization, and Fade In/Out.
- **Clipping** – distortion caused by recording or boosting audio past the maximum level.
- **Labels** – markers added to the timeline to flag edits or sections.
- **Projects vs exports** – `.aup3` projects keep your edits; exports create a playable file such as MP3 or WAV.

---

## Useful Shortcuts

| Shortcut | Action |
|---|---|
| `R` | Start recording |
| `Space` | Play / stop |
| `Ctrl/Cmd + Z` | Undo |
| `Ctrl/Cmd + K` | Silence selection |
| `Ctrl/Cmd + T` | Trim to selection |
| `Ctrl/Cmd + I` | Split at cursor |
| `Ctrl/Cmd + Shift + E` | Export audio |

---

## Example Walkthrough

Recording and cleaning a voice-over:

1. Click **Record** to capture audio from your microphone, then click **Stop**.
2. Select a few seconds of room silence and choose **Effect → Noise Reduction → Get Noise Profile**.
3. Select the whole track and apply **Effect → Noise Reduction** to remove steady background hiss.
4. Cut out mistakes by selecting them and pressing `Delete`.
5. Apply **Effect → Compressor** to even out loud and quiet parts.
6. Apply **Effect → Normalize** (or **Loudness Normalization**) to bring the overall level to a consistent volume.
7. Add a **Fade In** and **Fade Out** to the start and end.
8. Export with **File → Export Audio** as WAV for editing or MP3 for sharing.

---

## Tips and Common Mistakes

- **Record at a healthy level.** Peaks around −12 dB to −6 dB leave headroom and avoid clipping.
- **Record in a quiet room** — it is much easier to avoid noise than to remove it.
- **Don't over-apply noise reduction**, which can leave voices sounding watery.
- **Keep the original recording** and work on copies.
- **Save a project** (`.aup3`) in addition to exporting, so edits remain adjustable.

---

## When To Choose Audacity

Choose Audacity for simple recording, voice editing, and quick cleanup. For syncing audio to picture, a video editor such as [DaVinci Resolve](../Video%20Production/DaVinciResolve.md) includes its own audio tools (the Fairlight page). For full music production with virtual instruments, you would move to a dedicated digital audio workstation.

---

## Where To Go Next

- Record a one-minute podcast intro and practice noise reduction and compression.
- Layer a voice track over background music and balance the volumes.
- Learn about LUFS loudness targets so your audio matches podcast and streaming standards.

[⬅ Back to Sound Production Fundamentals](./[0]-Introduction-to-SoundProduction.md)