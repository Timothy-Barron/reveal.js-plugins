# Chalkboard Plugin Changelog

## 2.3.3 (modified) — Timothy Barron fork

### New features

- **Surface Pen eraser tip support**: The eraser end of a Surface Pen (or any pen device reporting `buttons & 32`) activates erase mode automatically. Note: Apple Pencil has no eraser tip/button and never sets this flag in Safari/WebKit, so this specific detection does not apply on iPad — use the native pencil gesture bridge below or the on-screen eraser/toggle button instead.
- **Native pencil gesture support** (`pencilDoubleTap` / `pencilSqueeze` window events): Double-tap and squeeze aren't exposed to web content by Safari/WebKit, but a native app wrapper (e.g. a SwiftUI `WKWebView` host using `onPencilDoubleTap`/`onPencilSqueeze`) can forward them as custom DOM events. `pencilDoubleTap` toggles the eraser. `pencilSqueeze` erases only while held, keyed off `detail.phase` matching SwiftUI's `PencilSqueezePhase`: `'active'` starts erasing, `'ended'`/`'failed'` restores the previous color; any other/missing phase falls back to a simple toggle.
- **Configurable erase mode** (`eraserMode` config option):
  - `'pixel'` — sponge/circular area erase (default)
  - `'object'` — stroke erase: touching any part of a stroke removes it whole (PowerPoint-style)
  - `'user'` — shows a toggle button in the palette so the user can switch between modes at runtime
- **Sponge cursor matches erase area**: The cursor image is generated at runtime as a circle scaled to `eraser.radius`, so the visible cursor and the erased area are always the same size and perfectly aligned.
- **Distinct cursor for stroke erase mode**: A small circle-outline cursor (sized to `eraser.strokeRadius`) is generated for stroke/object mode, visually distinguishing it from the filled sponge cursor. The stroke hit-test radius defaults to `5` for precise point-and-click erasure; configure via `eraser: { strokeRadius: N }`.
- **Public API additions**: `eraseStrokeAtPage(pageX, pageY)`, `setEraserRadius(r)`, and `setStrokeEraserRadius(r)` exposed on `window.RevealChalkboard`.

### Bug fixes

- Fixed `div.overlay` CSS positioning so the drawing canvas correctly covers the full viewport.
- Fixed `eraseWithSponge()` so the cleared circle is centered on the pointer position rather than offset to the bottom-right.
- Fixed `colorIndex()` public wrapper to forward its argument.
- Fixed `changeCursor()` to guard against an undefined tool object.
- Fixed `drawWithChalk()` to guard against a negative color index (eraser mode).
- Fixed `configure()` to merge `config.eraser` with `Object.assign` instead of replacing the whole object; the original code discarded `eraser.src` when the user supplied a partial eraser config, which became critical once `createSpongeCursor()` depended on `eraser.src` at runtime.
- Fixed `initStorage()` to distinguish between no saved data (normal first-run, logged at `console.log`) and corrupt/unparseable data (logged as `console.warn` with the error attached).
- Fixed purple boardmarker color: `rgba(150,0,20150,1)` → `rgba(150,0,150,1)` (pre-existing typo caused it to render as white).

### Asset changes

- `img/sponge.png` cropped from 40×50 to 40×40 (removed transparent bottom padding).
