# TERRASCII v2.0 — Unified Edition

> A self-contained ASCII tilemap editor for game designers, writers, and worldbuilders — works on desktop and mobile from a single file.

**Canonical file:** `mobile/index.html`
This is the active, unified codebase for all TERRASCII development. It replaces the separate `desktop/` version and supports both desktop and mobile interfaces from one HTML file.

No build tools. No dependencies. No server. Download one HTML file, open it in any modern browser on any device, and start mapping.

---

## What Is It?

TERRASCII is a browser-based ASCII tilemap editor built entirely in vanilla HTML, CSS, and JavaScript. The entire application ships as a **single `.html` file**.

It supports two interface modes in the same file:

| Mode | Interface | Best for |
|---|---|---|
| 🖥 **Desktop** | Classic fixed sidebar + keyboard/mouse controls | Wide-screen browsers, power users |
| 📱 **Mobile** | Bottom toolbar + swipe-up sheets + touch gestures | Phones, tablets, touch screens |

Switch modes at any time using the **🖥 / 📱 toggle** in the header, or append `?ui=desktop` / `?ui=mobile` to the URL. Your preference is saved automatically.

---

## Features

### Canvas & Editing
- **Configurable grid** — set the number of tiles (cols × rows), tile dimensions in cells, and pixel size per cell
- **Four brush modes** — Paint, Erase, Flood Fill, and Select
- **Variable brush sizes** — 1×1, 3×3, 5×5, and 7×7 cell brushes
- **4-connected and 8-connected flood fill** — toggle diagonal fill behavior
- **Rectangular selection** with Copy, Cut, and Paste (four placement modes)
- **Per-cell color overrides** — individual cells can have colors independent of their palette defaults
- **Undo / Redo** (Ctrl+Z / Ctrl+Y) — full snapshots before every major operation, 50-step stack
- **Coordinate status bar** — shows tile, cell, and world coordinates on hover

### Symbol Palette
- **Fully editable palette** — add, remove, and reorder categories and symbols
- **Per-symbol color overrides** alongside category-level defaults
- **Color clipboard** — copy a hex color from one symbol and paste it to another
- **Active symbol indicator** always visible

### Layers
Three independent layers stack on top of each other — each has its own palette, map data, and cell color overrides:

| Layer | Badge | Default palette | Purpose |
|---|---|---|---|
| **Terrain** | `T` (green) | Land, water, elevation, vegetation | Base world geography |
| **Underground** | `U` (purple) | Walls, floors, dungeon features | Caves, dungeons, interiors |
| **Structures** | `S` (amber) | Buildings, roads, town features | Towns, roads, surface structures |

- Click a layer row in the sidebar (desktop) to make it active — the palette and brush switch to that layer
- The eye button (◉/○) toggles layer visibility
- Layer names are editable inline
- Rendering composites layers bottom-up: Terrain → Underground → Structures. Empty cells (space) in upper layers are transparent — the layer below shows through.
- Layer data is saved with the project (v2 schema)

### Terrain Generation
Open the **Generate** modal to fill tiles procedurally. Five tabs:

**Weighted Random** — assign percentage weights to symbols. Live CDF bar, coherence smoothing, overwrite/skip modes.

**Perlin Noise** — dual-handle noise bands per symbol, per-symbol enable/disable, scale/octaves/seed/coherence controls. Random Bands and Reset buttons.

**Water** — two ordered passes:
1. **Bodies of Water** — Poisson-disk sampled lakes with user-defined depth rings (center → shore), Perlin-wobbled organic shapes
2. **Rivers & Streams** — explicit source → destination pairs, noise-guided meander + destination pull. Click cells on the map to set points.

**Roads & Paths** — generates roads and paths on the active structure layer (Underground or Structures). Uses the same noise-guided pathfinding as Rivers. Define source → destination pairs; roads use horizontal/vertical symbols, paths use a diagonal/winding symbol. The Terrain layer is never modified by this generator.

### Generation Presets
- Save named presets capturing all three tabs
- Presets travel with the project `.json`
- Export/import presets as standalone `.json` files

### Export & Project Management
| Action | Result |
|---|---|
| **Save Project** | Downloads a `.json` with full project state |
| **Open Project** | Loads a previously saved `.json` |
| **New Project** | Resets to a fresh grid |
| **Save Map** | Downloads the active tile as `.txt` |
| **Export Maps** | Downloads every tile as individual `.txt` files |
| **Copy Map** | Copies the active tile text to the system clipboard |
| **Legend** | Exports the symbol palette as a reference `.json` |

**Autosave** runs automatically (debounced, 900 ms) via `localStorage`. Your work is restored the next time you open the file in the same browser.

### Screenshot Mode
Hides all UI chrome for clean screenshotting. Press **Escape** or click the exit button to return.

---

## Getting Started

1. Open `mobile/index.html` in any modern browser (Chrome, Firefox, Safari, Edge)
2. On a wide screen it defaults to desktop mode (sidebar). On a phone/tablet it defaults to mobile mode (bottom toolbar).
3. Use the **🖥 / 📱** toggle in the header to switch at any time.
4. Start painting!

---

## Keyboard Shortcuts (Desktop Mode)

| Shortcut | Action |
|---|---|
| `Ctrl + Z` | Undo |
| `Ctrl + Y` | Redo |
| `Ctrl + C` | Copy selection |
| `Ctrl + X` | Cut selection |
| `Ctrl + V` | Open Paste modal |
| `Ctrl + S` | Save project |
| `Escape` | Clear selection / Exit screenshot mode |

---

## Architecture

See [ARCHITECTURE.md](./ARCHITECTURE.md) for a full technical breakdown.

In brief:
- **Pure HTML + CSS + vanilla JS** — no frameworks, no modules, no build step
- **Single self-contained file** — open directly in any modern browser
- **Interface mode system** — `body.ui-desktop` / `body.ui-mobile` CSS classes + `applyUIMode()` JS function
- **All state is module-level variables** — no classes, no global state objects
- **Seeded LCG + Perlin noise** — fully self-contained generation algorithms
- **CSS custom properties** (`--bg`, `--text`, `--accent`, …) for theming

---

## Browser Compatibility

Any modern browser with ES2020 support. No polyfills required.

- Chrome 88+, Firefox 85+, Safari 14+, Edge 88+

Running from a local `file://` path disables `localStorage` autosave in some browsers. Serving over `http://localhost` (e.g. `npx serve .`) restores autosave.

---

## Contributing

Issues and pull requests are welcome. All work goes into `mobile/index.html` — the `desktop/` directory is archived as v1.0 and is no longer maintained.

Please refer to [ARCHITECTURE.md](./ARCHITECTURE.md) and `.github/` for coding standards (vanilla JS, single-file constraint, ESLint).

---

## License

MIT — see [desktop/LICENSE](../desktop/LICENSE) for details.

---

### AI Assistance Notice
TERRASCII was designed, directed, and authored by Pablo (Furious Quan). Coding was done by Claude Sonnet. All architecture, features, algorithms, and design decisions were reviewed by the author.

---

## Contact

[furioquan@gmail.com](mailto:furioquan@gmail.com)  
[X](https://x.com/waywardisopod)
