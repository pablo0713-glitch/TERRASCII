# TERRASCII — Unified Architecture

> **Canonical file:** `mobile/index.html`
> This is the single source of truth for all TERRASCII development going forward.
> The `desktop/` directory is archived as v1.0 and is no longer actively maintained.

---

## The "Single File" Principle

TERRASCII is intentionally designed as a zero-dependency, serverless application. The entire software (UI, state management, generation logic, file I/O) is contained within a **single HTML file** (`mobile/index.html`).

This is not a constraint born out of limitation, but a core product feature:
- Endless portability.
- Zero "NPM install" or "Webpack" steps for the end-user.
- Agnostic to hosting environments. It just works.

### Core Technologies
- **HTML5:** Semantic DOM elements.
- **Vanilla CSS:** No preprocessors (SASS/LESS), no massive framework bundles (Bootstrap/Tailwind) unless strictly loaded via CDN for a specific, isolated utility (like icons).
- **Vanilla JavaScript:** ES6+ syntax. No React, Vue, Svelte, or Angular.

---

## Interface Mode System (Desktop / Mobile)

`mobile/index.html` is the **unified** file — it supports both the classic desktop sidebar interface and the mobile bottom-toolbar + bottom-sheets interface from a single codebase.

### Switching Modes

| Method | How |
|---|---|
| **URL flag** | Append `?ui=desktop` or `?ui=mobile` to the file URL |
| **Header toggle** | Click the 🖥 / 📱 button in the top-right of the header |
| **Auto-detect** | On first load, viewports > 900 px default to desktop; narrower viewports default to mobile |
| **Persistence** | The chosen mode is saved to `localStorage` (`terrascii-ui-mode`) and restored on next open |

### Desktop Mode (`body.ui-desktop`)
- Classic fixed sidebar (260 px) with **Layers**, **Brush**, and **Symbols** panels
- Keyboard shortcuts active (Ctrl+Z/Y/C/X/V/S)
- Mouse wheel + Ctrl for zoom
- `interactionMode` is forced to `'paint'` on entry so brush tools work immediately

### Mobile Mode (`body.ui-mobile`)
- Bottom toolbar: active symbol indicator + Paint/Erase/Fill/Select buttons + Tools toggle
- Bottom sheets: Palette sheet (full symbol grid) + Tools sheet (all actions)
- Interaction mode strip: Pan/Select ↔ Paint toggle
- Pinch-to-zoom and single-finger pan on the canvas
- Touch-optimised tap targets (44 px minimum)

### CSS Architecture
Mode switching is implemented entirely via two body classes:

```css
body.ui-desktop  /* shows sidebar, hides bottom toolbar + mode strip */
body.ui-mobile   /* hides sidebar, shows bottom toolbar + mode strip */
```

The `applyUIMode(mode)` function applies the class, syncs the toggle buttons, and — when entering desktop mode — forces `interactionMode = 'paint'` and calls `setMode('paint')` so the sidebar brush buttons are immediately active.

---

## Layer System

TERRASCII v2.0 introduces a **three-layer compositing system**. Each layer is an independent editing surface with its own palette, map data, and cell color overrides.

### Layer Stack (bottom → top)

| Index | ID | Name | Default palette |
|---|---|---|---|
| 0 | `terrain` | Terrain | Land, water, elevation, vegetation |
| 1 | `underground` | Underground | Walls, floors, dungeon features |
| 2 | `structures` | Structures | Buildings, roads, town features |

### Layer Object Shape

```js
{
  id:         'terrain',   // fixed identifier
  name:       'Terrain',   // user-editable display name
  visible:    true,        // toggles composite rendering
  categories: [...],       // palette categories (same schema as v1)
  maps:       [...],       // 2D char arrays, one per tile
  cellColors: {},          // per-cell color overrides: "mi,r,c" → hex
}
```

### Backward-Compat Shims

Existing code that reads `categories`, `maps`, and `cellColors` directly continues to work via three `let` variables that are always kept in sync with the active layer:

```js
let categories = [];
let maps       = [];
let cellColors = {};
```

`syncLayerRefs()` re-points these variables to the active layer's data. It must be called after:
- Layer switch (`setActiveLayer()`)
- Project load (`applyProjectJSON()`)
- New project (`initLayers()`)
- Grid config reset

### Composite Rendering

`refreshMapComposite(mi)` renders a tile by walking layers **bottom-up** (terrain → underground → structures) and displaying the topmost visible non-empty character for each cell. Empty cells (`' '`) in upper layers are transparent — the layer below shows through.

`refreshMap(mi)` (single-layer fast path) is still used for painting and generation on the active layer, since it only needs to show that layer's data.

### Layer Panel (Sidebar)

`buildLayerPanel()` inserts a `LAYERS` section at the top of the sidebar. Each row has:
- **Eye button** (◉/○) — toggles `layer.visible`, triggers `refreshMapComposite()` on all tiles
- **Name input** — editable inline, updates `layer.name`
- **Type badge** — `T` / `U` / `S` with colour-coded border

Clicking a row calls `setActiveLayer(idx)`, which calls `syncLayerRefs()`, resets the current symbol to the first in the new layer's palette, and rebuilds the palette panel.

---

## State Management

State is managed by module-level JavaScript variables inside the `<script>` tag.

### Core State Variables

| Variable | Type | Description |
|---|---|---|
| `GRID_COLS`, `GRID_ROWS` | `number` | Number of tiles across/down |
| `TILE_W`, `TILE_H` | `number` | Tile dimensions in cells |
| `CELL_PX` | `number` | Rendered pixel size per cell |
| `layers[]` | `Array<Layer>` | All three layer objects |
| `activeLayerIdx` | `number` | Index of the active layer (0–2) |
| `categories` | `Array` | Shim → `layers[activeLayerIdx].categories` |
| `maps[]` | `Array` | Shim → `layers[activeLayerIdx].maps` |
| `cellColors{}` | `Object` | Shim → `layers[activeLayerIdx].cellColors` |
| `mode` | `string` | `'paint' \| 'erase' \| 'fill' \| 'select'` |
| `interactionMode` | `string` | `'pan' \| 'paint'` (touch intent) |
| `undoStack` / `redoStack` | `Array` | Capped JSON snapshot arrays (50 max) |
| `genPresets[]` | `Array` | Saved generation presets (persisted with project) |
| `riverPairs[]` | `Array` | Water tab source→destination pairs |
| `roadPairs[]` | `Array` | Roads tab source→destination pairs |

Local persistence is achieved via `localStorage`, serializing the core state on a debounced timer (900 ms after a change).

---

## Render Cycle

We avoid "Virtual DOM" reconciliation. Instead, we use tight, direct DOM manipulation:

1. **The Canvas:** Rendered via standard DOM elements (`div`, CSS Grid). Each cell is a `<div class="cell">` with `data-mi`, `data-r`, `data-c` attributes.
2. **Cell updates:** When a cell changes, only the specific DOM node is updated (text content + color). Full re-renders are only triggered on major operations (generation, paste, undo/redo, project load).

### Render Functions

| Function | When used |
|---|---|
| `refreshMap(mi)` | Single-layer fast path — painting, generation on active layer |
| `refreshMapComposite(mi)` | Multi-layer composite — layer visibility toggle, Roads generation, project load |

---

## Touch & Pointer Event Architecture

All touch handling is done via three document-level listeners (`touchstart`, `touchmove`, `touchend`) rather than per-cell listeners. This avoids event-bubbling conflicts and allows:
- **Pinch-to-zoom** (2 fingers) — scales the world-grid via CSS `transform: scale()`
- **Pan** (1 finger, pan mode) — scrolls `canvas-wrap` directly
- **Paint stroke** (1 finger, paint mode) — uses `elementFromPoint()` to find the cell under the finger

Mouse events use per-cell `mousedown` / `mouseenter` listeners with a document-level `mouseup` to end strokes.

---

## Generation Algorithms

All generation is self-contained in the single file:

| Algorithm | Used for |
|---|---|
| Seeded LCG | Reproducible randomness for all generators |
| `makePerlin()` / `octaveNoise()` | Perlin noise terrain + river/road meander |
| Iterative flood fill (4- or 8-connected) | Flood Fill brush mode |
| Poisson-disk sampling | Even lake placement |
| Noise-guided pathfinding + destination pull | Rivers, streams, roads, paths |
| 4-step backtrack memory | Prevents river/road self-crossing |
| `worldGet()` / `worldSet()` / `worldSetForce()` | Cross-tile coordinate access |
| Majority-rule smoothing | Coherence passes after generation |

### Generate Modal Tabs

| Tab | Generator | Target layer |
|---|---|---|
| Weighted | `runGeneration()` weighted branch | Active layer (terrain only recommended) |
| Perlin | `runGeneration()` perlin branch | Active layer (terrain only recommended) |
| Water | `runWaterGen()` | Active layer (terrain) |
| Roads & Paths | `runRoadsGen()` | Active structure layer (Underground or Structures) |
| Presets | — | Saves/loads all tab state |

### Roads & Paths Generator

`runRoadsGen()` reuses the same noise-guided pathfinding as rivers but:
- **Writes to `layers[activeLayerIdx].maps`** directly — never touches the Terrain layer
- Uses `refreshMapComposite()` after generation so all layers render correctly
- Supports two route types: **Road** (horiz/vert symbols) and **Path** (diagonal/winding symbol)
- Guarded: refuses to run if `activeLayerIdx === 0` (Terrain layer)
- Road/path pairs are stored in `roadPairs[]` (separate from `riverPairs[]`)
- Pick mode (`roadPickPending`) uses the same transparent-modal + cell-click mechanism as river picks

---

## Project File Format

Projects are saved as JSON. Two schema versions are supported:

### v2 (current — with layers)

```json
{
  "version": 2,
  "name": "my-world",
  "gridCols": 3,
  "gridRows": 3,
  "tileW": 18,
  "tileH": 18,
  "cellPx": 22,
  "activeLayerIdx": 0,
  "layers": [
    {
      "id": "terrain",
      "name": "Terrain",
      "visible": true,
      "categories": [ ... ],
      "maps": [ ... ],
      "cellColors": { "0,2,5": "#ff6b6b" }
    },
    { "id": "underground", ... },
    { "id": "structures",  ... }
  ],
  "genPresets": [ ... ]
}
```

### v1 (legacy — auto-migrated on load)

```json
{
  "version": 1,
  "name": "my-world",
  "gridCols": 3, "gridRows": 3,
  "tileW": 18, "tileH": 18, "cellPx": 22,
  "categories": [ ... ],
  "maps": [ ... ],
  "cellColors": { "0,2,5": "#ff6b6b" },
  "genPresets": [ ... ]
}
```

`applyProjectJSON()` detects `version: 1` and auto-migrates: the v1 data becomes the Terrain layer, and two empty structure layers (Underground, Structures) are created with their starter palettes.

---

## AI Agent Integration

We have specifically scoped out tools for AI agents (GitHub Copilot via VS Code) working on this project:
- **`terrascii.agent.md`**: Dictates that the agent must prioritize `mobile/index.html` as the unified platform and focus on responsive vanilla JS.
- **`terrascii.instructions.md`**: Enforces strict styling (single quotes, no inline CSS unless dynamic, descriptive comments, semantic HTML).
- **Linting Hooks**: Local ESLint (`eslint-plugin-html`) runs automatically when the agent completes an edit, rejecting syntax errors within the HTML file's script tags.

---

## Versioning

| Version | File | Status | Key additions |
|---|---|---|---|
| v1.0 | `desktop/index.html` | **Archived** — stable, no new features | Desktop-only, single layer |
| v2.0 | `mobile/index.html` | **Active** — unified desktop + mobile | UI mode switch, three layers, Roads & Paths generator, v2 project schema |
