# 🎬 Stop Motion Studio

A browser-based, frame-by-frame animation studio. Draw on a canvas, flip through frames with onion-skin ghosting to guide your poses, play the result back at an adjustable frame rate, and export it as a video — all in a single self-contained HTML file with no build step, server, or dependencies.

## Features

- **Frame-by-frame drawing** on an 800×600 canvas with a pen and eraser
- **Onion skinning** — the previous frame shows through at adjustable opacity so you can align each pose
- **Film strip** of live thumbnails for jumping between, adding, duplicating, and deleting frames
- **Playback** with an adjustable frame rate (1–24 FPS) and a looping preview
- **Brush controls** — 7 preset colors plus a custom color picker, and an adjustable brush size (1–40)
- **Per-frame undo** (up to 20 steps) and a one-click clear
- **Video export** to WebM via `MediaRecorder` (VP9/VP8)
- **Auto-save** to `localStorage` — your work is restored automatically when you reopen the page
- **Touch support** for drawing on tablets and touchscreens

## Getting started

No installation or build is required. Just open the file in a modern browser:

```bash
open index.html
```

Or serve it locally (any static server works):

```bash
python3 -m http.server
```

then visit `http://localhost:8000`.

## Usage

1. **Draw** on the white canvas with the pen. Pick a color and brush size from the top toolbar.
2. **Add a frame** with the `+` button in the film strip (or **Duplicate** the current frame to keep your drawing as a starting point).
3. Use the faint **ghost** of the previous frame to position your next pose, then draw the changes.
4. Repeat to build up your animation.
5. Press **▶ Play** to preview the loop, and adjust **FPS** to control the speed.
6. Click **💾 Save Video** to export a `stopmotion.webm` file.

Your animation is auto-saved to the browser and restored the next time you open the page. Use **🆕 New** to start over.

## Keyboard shortcuts

| Key | Action |
| --- | --- |
| `Space` | Play / pause |
| `E` | Toggle eraser |
| `Ctrl`/`Cmd` + `Z` | Undo (current frame) |
| `→` / `←` | Next / previous frame |
| `N` | New blank frame |
| `D` | Duplicate current frame |

## Browser support

Works in any modern browser. Video export requires the [`MediaRecorder` API](https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder) — best supported in Chrome and Firefox. If export is unavailable, you'll be prompted to switch browsers.

## How it works

Everything lives in [`index.html`](index.html) — HTML, CSS, and vanilla JavaScript, with no external libraries. Each frame is an off-screen `<canvas>`; the visible canvas composites a white background, the onion-skin ghost of the previous frame, and the current frame's drawing. Export replays the frames into an off-screen canvas whose `captureStream()` feeds a `MediaRecorder`, and frames are persisted as PNG data URLs in `localStorage`.

## License

BSD 3-Clause License. See [LICENSE](LICENSE).
