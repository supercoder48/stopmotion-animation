# CLAUDE.md

Guidance for working in this repository.

## What this is

A browser-based, frame-by-frame stop-motion animation studio. The **entire application lives in [`index.html`](index.html)** — HTML markup, CSS in a `<style>` block, and vanilla JavaScript in a `<script>` block. There are no dependencies, no build step, no package manager, and no server. Open the file in a browser and it runs.

## Running it

Just open the file:

```bash
open index.html
```

Or serve statically for testing (`python3 -m http.server`). There are no tests, linters, or CI configured.

## Architecture

Everything is in `index.html`. The JS is organized into commented sections (search for the `── SECTION ──` banners):

- **STATE** — module-level globals: `frames` (array of off-screen `<canvas>`), `currentIdx`, `tool`, `color`, `brushRadius`, `ghostOpacity`, `fps`, `undoStack`.
- **FRAME MANAGEMENT** — `makeBlankFrame`, `cloneFrame`, `addFrame(duplicate)`, `deleteFrame`, `switchTo`.
- **RENDER** — `render()` composites the visible canvas: white background → onion-skin ghost of the previous frame (when not playing) → current frame.
- **DRAWING** — pointer/touch handlers (`startDraw`/`draw`/`endDraw`) draw onto the current frame's off-screen canvas, then call `render()`.
- **PLAYBACK** — `startPlay`/`stopPlay` loop through frames on a `setInterval` timed to `fps`.
- **FILM STRIP** — thumbnail DOM built/updated by `rebuildStrip`, `updateThumb`, `highlightStrip`.
- **TOOLS** — toolbar wiring for color swatches, brush size, eraser, clear, undo, ghost opacity, FPS.
- **EXPORT (video / GIF)** — the `⬇ Export` button opens a small format menu. `exportWebM()` replays frames into an off-screen canvas whose `captureStream(fps)` feeds a `MediaRecorder` (`stopmotion.webm`); `exportGIF()` composites each frame and calls the GIF encoder (`stopmotion.gif`). Shared helpers: `compositeFrameInto`, `frameImageData`, `downloadBlob`.
- **GIF ENCODER** — self-contained GIF89a encoder, no dependencies: `encodeGIF` (assembles header, global color table, Netscape loop block, per-frame Graphic Control + Image Descriptor, trailer), `buildPalette` + `medianCut` + `nearestIndex` (reduce to ≤256 colors with a cached nearest-color lookup), and `lzwEncode` (variable-width LZW). If you touch `lzwEncode`, re-verify by decoding the output through an `<img>` and comparing pixels — the code-size-growth and 4096-entry-reset logic must stay in sync with the decoder.
- **PROJECT SAVE / IMPORT** — `saveProjectBtn` (labeled "💾 Save") downloads the project as JSON (`{app, version, width, height, fps, currentIdx, frames:[dataURL]}`); `importBtn`/`importFile` (labeled "📂 Load") reads a project file and calls `loadProjectData`. `framesFromDataURLs` and `loadProjectData` are shared with `restoreFrames`.
- **KEYBOARD SHORTCUTS** — `Space` play/pause, `E` eraser, `Ctrl/Cmd+Z` undo, arrows change frame, `N` new frame, `D` duplicate.
- **AUTO-SAVE** — debounced `persistFrames` writes frames as PNG data URLs to `localStorage` under key `stopmotion_v1`; `restoreFrames` reloads them on init via `loadProjectData`.

## Key facts and conventions

- Canvas is a fixed `FRAME_W`×`FRAME_H` (800×600); `resizeDisplay()` only scales the CSS display size, never the backing resolution. Drawing coordinates are mapped back to canvas space in `getCanvasPos`.
- Each frame is a separate off-screen canvas kept transparent — the white background is drawn by the display canvas so the ghost can show through.
- Undo is **per-frame**: `undoStack` is an array parallel to `frames`, each holding up to `MAX_UNDO` (20) `ImageData` snapshots. `saveUndo()` must be called *before* a mutating draw.
- The eraser uses `globalCompositeOperation = 'destination-out'`; always reset it to `'source-over'` after erasing.
- When adding/removing frames, keep `frames` and `undoStack` in sync (both are spliced together).
- Match the existing style: plain DOM APIs, no frameworks, section banner comments, and 2-space indentation.
