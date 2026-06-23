---
name: erm-filler-remover
description: >-
  Strip filler words and disfluencies (um, uh, er, erm, ah, hmm, mhm, mm,
  uh-huh, and elongations) from speech recordings using the erm CLI. Covers
  install, basic clean, video sync, and output tuning/troubleshooting.
---

# erm -- Remove Filler Words from Speech Recordings

A unified skill for **erm**, the local CLI that removes disfluencies from
English speech audio. It transcribes with faster-whisper, runs audio-domain
detectors for fillers Whisper misses, and splices with ffmpeg (energy-snapped,
crossfaded, room-tone-matched).

Provenance:
- Source repository: https://github.com/dougcalobrisi/erm
- Original sub-skills: `skills/erm/SKILL.md` (install & run) and `skills/erm-tune/SKILL.md` (diagnose & tune)
- Docs: https://dougcalobrisi.github.io/erm/

## When to use this skill

Use when the user wants to:
- Install or set up erm
- Clean up a recording, podcast, voiceover, or presentation audio
- Strip "ums", "uhs", filler words from audio or video
- Process video while keeping A/V sync
- Fix imperfect erm output (missed fillers, clipped words, clicking splices, noise floor pumps)
- Tune detection thresholds, crossfade, denoise, or room-tone settings

## Sub-skill routing

This skill has two modes. Route based on the user's situation:

### Mode 1: Install & Clean (erm)
**When:** First-time setup, basic filler removal, choosing a command.
**Source:** https://github.com/dougcalobrisi/erm/blob/main/skills/erm/SKILL.md

Core workflow:
1. Install: needs Python 3.11+ and ffmpeg. Prefer `uvx erm` (no persistent install); fall back to venv + `pip install erm`.
2. Ask (only if unclear): audio type (podcast/video/multitrack/studio), render mode (remove vs silence), video handling (audio-only vs `--video`).
3. Dry-run: `erm INPUT.wav --dry-run` to inspect the cut list.
4. Render: `erm INPUT.wav` produces `INPUT-cleaned-<timestamp>.wav`.
5. Validate: `erm validate INPUT.wav OUTPUT.wav` -- re-transcribes output, asserts no fillers survive.

Key flags: `-o/--output`, `--dry-run`, `--json`, `--model`, `--device`, `--video`, `--mode {remove,silence}`, `--add-fillers`, `--remove-fillers`, `--fillers`.

Video: by default erm emits cleaned audio only. Add `--video` to render the picture too (container inferred from input, A/V in sync). `--mode silence` stream-copies the picture losslessly.

### Mode 2: Tune & Troubleshoot (erm-tune)
**When:** Output is imperfect, user wants to adjust settings.
**Source:** https://github.com/dougcalobrisi/erm/blob/main/skills/erm-tune/SKILL.md

Diagnose by symptom, then adjust the matching knob cluster:

| Symptom | Cluster | Key flags |
|---|---|---|
| Fillers still audible | Detection | `--model`, `--detect-gaps`, `--confirm-pitch`, `--gap-min-ms` |
| Extra word to cut / default over-matches | Detection word list | `--add-fillers`, `--remove-fillers` |
| Real words clipped | Detection / Refinement | reduce aggressiveness, `--search-ms`, `--merge-gap-ms` |
| Splices click or sound smeared | Crossfade | `--crossfade-factor`, `--min-crossfade-ms`, `--max-crossfade-ms` |
| Noise floor pumps | Denoise / Room tone | `--denoise`, `--room-tone`, `--room-tone-level-db` |
| Words run together | Splice spacing | `--pad-pause-factor`, `--pad-min-ms`, `--min-gap-ms` |

Iterate: tune detection with `--dry-run` first (cheap), change one cluster, re-render, validate.

## Resolving documentation

When you need authoritative detail, resolve in order:
1. `erm --help` and `erm validate --help` -- definitive flags and defaults
2. Public docs: https://dougcalobrisi.github.io/erm/ -- usage, recipes, troubleshooting, detection, render-pipeline, denoise-and-room-tone
3. Never guess flag names or defaults -- read the docs first

## Required inputs

- An audio or video file containing English speech with filler words
- For tuning: the original input plus the imperfect output, and a description of the symptom

## Output

- Cleaned audio file (`*-cleaned-*.wav`) or video (`*-cleaned-*.mp4`)
- Cut-list JSON (`*-cuts-*.json`) showing what was removed
- Validation pass/fail from `erm validate`
