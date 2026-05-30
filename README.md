# TERRASCII v3.0 — Unified Edition

> A self-contained ASCII tilemap editor for game designers, writers, and worldbuilders — works on desktop and mobile from a single file.

**Canonical file:** `TERRASCII/index.html`
This is the active, unified codebase for all TERRASCII development and supports both desktop and mobile interfaces from one HTML file.

No build tools. No dependencies. No server. Free for anyone to use, share, and adapt under the project license. Download one HTML file, open it in any modern browser on any device, and start mapping.

---

## What Is It?

TERRASCII is a browser-based ASCII tilemap editor built entirely in vanilla HTML, CSS, and JavaScript. The entire application ships as a **single `.html` file**.

It supports two interface modes in the same file:

| Mode | Interface | Best for |
|---|---|---|
| 🖥 **Desktop** | Left tools column + center canvas + dominant right Symbols panel | Wide-screen browsers, power users |
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
- **Canvas zoom controls** — Ctrl/⌘ + wheel to zoom; sidebar controls show the current percent and reset to 100%
- **Desktop pan gesture** — hold Shift and drag with the mouse to pan the canvas

### Symbol Palette
- **Symbol Palette Builder** — add, remove, reorder, and reset categories and symbols
- **Active-layer scoped** — loads and saves the palette for the currently selected layer only
- **Palette import/export** — load or save as `.json`, `.txt`, or `.html`
- **Top toolbar controls** — Load, Save, Reset to default, and Add Category are grouped together
- **Per-symbol color overrides** alongside category-level defaults
- **Color clipboard** — copy a hex color from one symbol and paste it to another, with its own status row
- **Active symbol indicator** always visible
- **Desktop Symbols panel** — the active layer palette is shown as a large right-side panel, separate from the left tool column
- **Brush Mix** — select multiple symbols, assign weights, and paint/fill with weighted random variation per cell
- **Symbol reference modal** — curated TERRASCII examples plus a complete printable ASCII/reference list, with copy and `.txt`/`.html` export actions
- **Expanded starter palettes** — Terrain, Underground, and Structures ship with richer, layer-specific defaults; restored old projects receive missing starter symbols without overwriting custom palette entries

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
- Fresh projects start with Terrain visible by default; Underground and Structures are hidden until you enable them
- Rendering composites layers in this order: Underground → Terrain → Structures. Empty cells (space) in upper layers are transparent, so lower layers show through.
- Layer data is saved with the project (v2 schema)

### Biome / Narrative Metadata
Symbol names can optionally encode engine-facing biome composition metadata:

```text
Mixed Pine & Hardwood | oak:30, pine:70
Oak-Hickory | oak, hickory
Forest Edge | grass:40, shrubs, saplings
```

When exported to JSON, TERRASCII keeps the display name and adds parsed `biomeData`:

```json
{
  "char": "T",
  "name": "Mixed Pine & Hardwood",
  "biomeData": {
    "oak": 30,
    "pine": 70
  }
}
```

Explicit weights use `:` or `=`. Unweighted entries split the remaining weight evenly. The editable project/autosave state keeps the original palette string; downloadable JSON exports are enriched for downstream engines.

### Terrain Generation
Open the **Generate** modal to fill tiles procedurally. Five tabs:

**Weighted Random** — assign percentage weights to symbols. Live CDF bar, coherence smoothing, overwrite/skip modes.

**Perlin Noise** — dual-handle noise bands per symbol, per-symbol enable/disable, scale/octaves/seed/coherence controls. Random Bands and Reset buttons.

**Water** — two ordered passes:
1. **Bodies of Water** — Poisson-disk sampled lakes with user-defined depth rings (center → shore), Perlin-wobbled organic shapes
2. **Rivers & Streams** — explicit source → destination pairs, noise-guided meander + destination pull. Click cells on the map to set points. Rivers and streams each have editable route styles for horizontal, vertical, corner, junction, and diagonal segments; symbols can be reused while color distinguishes river vs. stream output.

**Roads & Paths** — generates roads and paths on the active layer: Terrain, Underground, or Structures. Uses the same noise-guided pathfinding as Rivers. Define source → destination pairs; roads and paths each have editable route styles for horizontal, vertical, corner, junction, and diagonal segments.

For more natural meanders, prefer several shorter connected river/road routes over one very long route. Long routes are pulled strongly toward a distant destination; chained shorter segments usually produce cleaner bends while still forming a continuous path.

### Generation Presets
- Save named presets capturing the generator tabs
- Live river/stream and road/path route pairs are saved with the project/autosave state
- Route styles for flowing water, roads, and paths are saved with presets
- Presets travel with the project `.json`
- Export/import presets as standalone `.json` files

### Export & Project Management
| Action | Result |
|---|---|
| **Save Project → JSON** | Downloads a layered `.json` using the current v2 project schema, with all maps/layers and export-time symbol metadata parsing |
| **Save Project → TXT** | Downloads all non-empty maps as plain text, grouped by layer, with a symbol summary |
| **Save Project → HTML** | Downloads all non-empty maps as standalone HTML with inline color spans, grouped by layer, with a symbol summary |
| **Open Project** | Loads a previously saved `.json` |
| **New Project** | Resets to a fresh grid |
| **Save Selected Map → JSON** | Downloads the selected tile using the project JSON schema, scoped to the active layer |
| **Save Selected Map → TXT** | Downloads the selected tile as plain text from the active layer only, with a symbol summary |
| **Save Selected Map → HTML** | Downloads the selected tile as standalone HTML with inline color spans from the active layer only, with a symbol summary |
| **Copy Map** | Copies the active tile text to the system clipboard |

Project-wide text and HTML saves are separated by layer and suppress empty maps. Selected-map saves only include the currently selected map and active layer. JSON selected-map saves keep `gridCols` and `gridRows` so importing tools can place the tile back into the full project grid, and they include only the palette symbols actually used in that selected tile.

Manual project and selected-map JSON downloads include parsed `biomeData` for symbols using the `Display | species:weight` syntax. Autosave remains a lossless editor-state save and preserves the original editable symbol names.

**Autosave** runs automatically via `localStorage`: ordinary edits are debounced, generation saves immediately, and pending changes are flushed before refresh/navigation. Your work is restored the next time you open the file in the same browser.

### Screenshot Mode
Hides all UI chrome for clean screenshotting. Press **Escape** or click the exit button to return.

---

## Getting Started

1. Open `index.html` in any modern browser (Chrome, Firefox, Safari, Edge)
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
| `Ctrl/⌘ + Mouse Wheel` | Zoom canvas |
| `Shift + Left Drag` | Pan canvas |
| `Escape` | Clear selection / Exit screenshot mode |

---

## Architecture

See [FEATURES.md](./FEATURES.md) for a complete product-level feature inventory.

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

Issues and pull requests are welcome. All active work goes into `TERRASCII/index.html`.

Please refer to [ARCHITECTURE.md](./ARCHITECTURE.md) and `.github/` for coding standards (vanilla JS, single-file constraint, ESLint).

---

## License

MIT — see [LICENSE](./LICENSE) for details.

---

### AI Assistance Notice
TERRASCII was designed, directed, and authored by Pablo (Furious Quan). Coding was done by Claude Sonnet. All architecture, features, algorithms, and design decisions were reviewed by the author.

---

## Contact

[furioquan@gmail.com](mailto:furioquan@gmail.com)  
[X](https://x.com/waywardisopod)
