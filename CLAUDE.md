# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-file Pomodoro Timer (番茄钟) app — open `index.html` in a browser, no build step or server needed.

## Development

- **Run**: Open `index.html` directly in a browser — no build tools, no dependencies.
- **No tests, no linting** — this is a pure static HTML/CSS/JS project.

## Architecture

`index.html` is self-contained with three sections:

### CSS (lines 7-239)
Dark theme (`#1a1a2e`), 520px centered container. Key visual classes: `.mode-btn` (duration presets), `.ctrl-btn` (play/pause/stop controls), `.noise-btn` (background noise picker), `.timer-ring` (SVG circle progress).

### JS Classes

1. **`NoiseEngine`** (line 309) — Web Audio API white noise generator with 10 presets: white, pink, brown, rain, ocean, fan, wind, stream, cricket, bowl. Each preset builds an audio node graph via internal methods like `_filter()`, `_noiseBuffer()`, `_gain()`. Public API: `play(type)`, `stop()`, `pause()`, `resume()`, `volume` setter.

2. **`PomodoroTimer`** (line 560) — Countdown timer using `setTimeout`-based scheduling (not `setInterval`). Public API: `setDuration(min)`, `start()`, `pause()`, `resume()`, `stop()`. Fires `onTick(remainingSec, totalSec)` every ~500ms and `onComplete()` when done.

3. **App glue code** (line 634) — Binds UI elements to the two engines. Mode buttons (25/15/5/30 min + custom input), control buttons (start/pause/resume/stop), noise grid toggle, volume slider. Uses `localStorage` key `pomodoro_noise` to persist last noise choice. Uses `Notification` API for desktop alerts. Completion chime is a C-E-G arpeggio played via inline `playChime()`.

### State flow
- Mode selection → `timer.setDuration()` → updates SVG progress ring and display
- Start → `autoPlayNoise()` resumes previous noise → `updateButtonStates()` enables pause/stop
- Pause/resume threads through both `PomodoroTimer` and `NoiseEngine`
- Stop → resets both engines, clears noise UI highlight
- Completion → increments session counter, fires notification, flashes timer green, plays chime, stops noise

## Git remotes

- `origin`: Gitee (`git@gitee.com:pei_bo_li_admin/clock.git`)
- `github`: GitHub (`git@github.com:jacklpb/clock.git`)
