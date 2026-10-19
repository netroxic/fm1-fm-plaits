# Generative FM-PLAITS — for the M-VAVE FM-1

by **@narti_live** (Instagram)

A one-voice generative synth for the FM-1: Mutable Instruments **Plaits**
synthesis engines, a **low pass gate**, and a **generative melody sequencer**
modelled on the Sting Max for Live device. No drums. Black screen, white /
yellow / orange / red.

The Plaits engines are C ports of the published Plaits source code
(© Emilie Gillet, MIT license — see `app/fpl_voice.c`). The `plaits_1.2.wav`
update file itself is STM32 machine code and cannot run on the FM-1's JieLi
chip; the sound here comes from re-implementing the algorithms. It is close
in spirit to Plaits, not a bit-exact copy.

## Knobs (same on every page)

| Knob | What it does |
|---|---|
| **PRESETS** | Sound engine (16) |
| **ALGORITHM** | Low pass gate: centre = pluck, clockwise = longer decay, counter-clockwise = slow attack (swell) |
| **SELECT** | Tempo |
| **MASTER** | Volume |

## Pages (SEQ button = next page, HOME = back to SOUND)

| Page | K1 | K2 | K3 | K4 |
|---|---|---|---|---|
| **SOUND** | Harmonics | Timbre | Morph | Freq (−2 … +2 octaves) |
| **SEQ** | Root | Scale | Density | Length (1–16 steps) |
| **GROOVE** | Speed (/16 /8 /4 /2 X1) | Triplet on/off | Chance (% of notes that play) | Slide % |
| **REVERB** | Mix | Size | Decay (100 = FREEZE) | Tone |
| **DELAY** | Mix | Time (1/16, 1/8T, 1/8, 3/16, 1/4, 1/2 — follows BPM) | Feedback | — |
| **MENU** | MIDI out on/off | LFO speed | LFO depth | — |

The SOUND page shows the engine, a live oscilloscope and the LPG shape.
The boot screen shows "BY: NARTI LIVE".

The generator is clocked by the FM-1's own sequencer (a silent clock note on
every 1/16 of track 1), so it follows PLAY/STOP, tempo, swing and external
MIDI clock; slower speeds and triplets are placed between those ticks.
The random LFO's slowest setting drifts over about 1½ minutes.

The reverb ("black hole" style: 8 modulated delay lines, dark damping,
FREEZE) and the ping-pong delay run at half rate on 16-bit memory to fit the
FM-1 (≈100 KB of RAM); on the FM-1 the longest delay is about 0.74 s.

## Engines

1 VA · 2 WAVESHAPE · 3 FM · 4 GRAIN · 5 ADDITIVE · 6 WAVETABLE · 7 CHORDS ·
8 VOWEL · 9 SWARM · 10 NOISE · 11 PARTICLES · 12 STRING · 13 MODAL ·
14 PHASE DIST · 15 TERRAIN · 16 CHIPTUNE (14–16 after the Plaits 1.2 engines).
The Plaits drum engines are left out.

Scales: MINOR, PHRYG, DORIAN, MAJOR, PENTA− (minor pentatonic),
PENTA+ (major pentatonic), WHOLE, CHROM.

## MIDI out

MENU page, K1 right = ON. The melody goes out on **channel 1** with accents
as velocity, plus MIDI clock, over USB, Bluetooth MIDI and (where fitted)
DIN/TRS. Switching it stops playback (stock FM-1 behaviour) — press PLAY again.
External MIDI clock in still works with the default sync settings.

## Easiest install (Mac or phone, no Windows): GitHub builds it

1. Sign in at <https://github.com> → **+** → **New repository** → name
   `fm1-fm-plaits`, **Public** → **Create repository**.
2. Click **uploading an existing file** → drag in `FM1_FM_PLAITS_v1_source.zip`
   (keep it zipped) → **Commit changes**.
3. **Add file → Create new file** → name it
   `.github/workflows/build.yml` → paste the contents of `build-firmware.yml`
   → **Commit changes**. The build starts by itself.
4. Open the **Actions** tab → wait for the green tick (about 10–15 min) →
   click the run → under **Artifacts** download `FM1-FM-PLAITS-firmware`.
5. On the Mac: back up projects/voices/samples in the M-VAVE workbench
   (<https://open.m-vave.com/fm1-workbench>), then unzip the download, open
   `FM1-Official-Open-Source.html` in **Chrome**, connect the FM-1 by USB and
   run the update. The same page can restore the original M-VAVE V15 firmware.

If the run turns red, open it, copy the last lines of the log and send them to me.

**Space note.** JieLi now only offers a newer compiler that makes bigger code
than the one M-Vave used; with it the original firmware alone almost fills the
FM-1. So this build is "FM-PLAITS only": the original editor pages (DX7 FM
editor, step/arp editors, preset browser, sound source and action menus) and
the 128 DX7 factory voices are left out. FM-PLAITS, the mixer (hold GLO),
project saving (SAVE), MIDI and Bluetooth are all there. M-Vave's update page
can always put the original firmware back.

## Build (Windows) and install

1. Install the **JieLi PI32v2 clang 4.0.1 r3** toolchain:
   <https://doc.zh-jieli.com/Tools/zh-cn/dev_tools/dev_env/jl_toolchain.html>
2. In PowerShell, inside this folder:
   ```powershell
   $env:FM1_PI32_TOOLCHAIN = 'C:\JL\pi32'
   $env:FM1_SOURCE_URL = 'https://github.com/<you>/fm1-fm-plaits'   # where you publish this source (GPL)
   .\build.bat --package
   ```
3. Back up projects/voices/samples in the M-VAVE workbench
   (<https://open.m-vave.com/fm1-workbench>).
4. Open `build\official-web\FM1-Official-Open-Source.html` in Chrome or Edge,
   connect the FM-1 by USB and run the update. The same page can restore the
   original M-VAVE V15 firmware.

Tested in the FM-1 browser simulator (same C sources), not yet on hardware.
If the build reports an SRAM/size error, or the FM-1 shows "AUDIO OVERLOAD",
tell me — the engine set can be trimmed.

Linux/macOS preview: `FM1_WASI_SDK=/path/to/wasi-sdk-27.0 tools/build-web-linux.sh`
→ `build/preview/index.html`.

## Files

- `app/fpl_voice.c/.h` — Plaits-derived engines + low pass gate + scope (audio, in SRAM)
- `app/narti.c/.h` — generative sequencer, random LFO, MIDI switch (control task)
- `app/narti_ui.inc` — screens and controls
- `app/screen.c`, `app/midi_router.c`, `app/audio_output.c` — small hooks

License: GPL-3.0 with the FM1 SDK linking exception; Plaits-derived code
keeps its MIT notice. Share the source if you share the firmware.

## Android app

`android/` builds the same instrument as a phone app (`FM-PLAITS.apk`,
Android 8.0+, 64-bit ARM phones and x86_64 emulators/Chromebooks). It is a
native app (no Java) with low-latency AAudio output and a touch UI:
engine picker, Harmonics/Timbre/Morph/LPG/Freq sliders, scope, the generative
melody (root keys, scale, octave, density, length) and NEW / MUTATE / BACK /
RANDOM / LFO / EVOLVE buttons. Hold LFO for LFO speed/depth and slide.
SPEED (/16 /8 /4 /2 X1) slows the sequencer down; FREQ moves in octaves (±2); CHANCE sets the probability of each note; 3 = triplets; REVERB and DELAY mix
sliders sit on the main screen and FX opens the full black-hole reverb (size,
decay up to FREEZE, tone) and synced ping-pong delay (time, feedback).
MIDI out is FM-1 only for now.

Build on Linux/macOS with LLVM (clang + ld.lld) and Python (cryptography,
Pillow): `android/build.sh` → `build/android/FM-PLAITS.apk`. No Android SDK
is needed. `FPL_KEY=/path/key.pem` picks the signing key (generated if absent;
keep it to install updates over an existing install).
