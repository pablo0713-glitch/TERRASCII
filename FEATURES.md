# TERRASCII v3.0 — Complete Features List

This document is a product-level inventory of TERRASCII's current capabilities. For implementation details, see [ARCHITECTURE.md](./ARCHITECTURE.md).

---

## Core Application

- Single-file browser app: all UI, state, generation, and file I/O live in `index.html`
- No build tools, dependencies, server, or install step required
- Runs from a local `.html` file in modern browsers
- Desktop and mobile interfaces are supported from the same file
- Interface mode can be selected by URL flag, header toggle, or automatic viewport detection
- UI mode preference is persisted in `localStorage`
- Local autosave restores the last browser session when available

---

## Desktop Interface

- Three-column workspace:
  - Left tool sidebar
  - Center map canvas
  - Right Symbols panel
- Top toolbar for project, grid, palette, symbols reference, generation, copy/paste, screenshot, undo, and redo
- Left sidebar contains:
  - Layer controls
  - Brush modes
  - Brush size
  - Zoom controls
  - Brush Mix controls
  - Fill connectivity
  - Selection copy/cut/paste actions
  - Clear map / clear all actions
- Right Symbols panel contains:
  - Active symbol or Brush Mix indicator
  - Brush Mix controls and weights
  - Full active-layer symbol palette
- Desktop keyboard shortcuts for common actions
- Ctrl/Command + mouse wheel zoom
- Shift + left-drag canvas panning
- Coordinate status readout for tile, cell, and world coordinates

---

## Mobile Interface

- Bottom toolbar with active symbol, Paint, Erase, Fill, Select, and Tools
- Bottom sheets for palette and tools
- Interaction strip for Pan/Select vs Paint intent
- Touch-first controls with larger tap targets
- Single-finger pan in pan mode
- Single-finger paint in paint mode
- Pinch-to-zoom
- Palette sheet includes Brush Mix controls and weight editing

---

## Canvas And Editing

- Configurable world grid:
  - Number of tiles across and down
  - Tile width and height in cells
  - Cell pixel size
- Multi-tile world canvas
- Active tile selection
- Paint mode
- Erase mode
- Flood Fill mode
- Select mode
- Brush sizes:
  - 1x1
  - 3x3
  - 5x5
  - 7x7
- Flood fill connectivity:
  - 4-connected cardinal fill
  - 8-connected diagonal fill
- Per-cell color overrides independent of palette defaults
- Direct DOM cell updates for fast painting
- Full map refreshes for generation, paste, undo, redo, and project load
- Undo and redo with capped snapshot stacks
- Screenshot mode hides UI chrome for clean captures

---

## Selection And Clipboard

- Rectangular tile selection
- Drag selection on the active tile
- Copy selected region
- Cut selected region
- Copy/cut full active tile when no selection exists
- Paste modal with placement options:
  - Top-left of active tile
  - At cursor position
  - Same position as copy source
  - Custom map and offset
- Paste preview via source and destination flash highlights
- System clipboard integration for copied map text
- Plain-text paste into active map

---

## Layers

- Three fixed compositing layers:
  - Terrain
  - Underground
  - Structures
- Each layer has independent:
  - Palette categories
  - Map data
  - Per-cell color overrides
  - Visibility state
- Layer visibility toggles
- Editable layer display names
- Active layer controls painting, palette, generation, and selected-map exports
- Composite rendering order:
  - Underground
  - Terrain
  - Structures
- Empty cells in upper layers are transparent
- Terrain starts visible by default
- Underground and Structures start hidden by default

---

## Default Starter Palettes

- Expanded built-in starter palettes for all layers
- Terrain starter palette includes:
  - Water
  - Land
  - Elevation
  - Vegetation
  - Features
  - Empty
- Underground starter palette includes:
  - Walls
  - Floors
  - Features
- Structures starter palette includes:
  - Buildings
  - Floors
  - Roads
  - Features
- Restored old projects are upgraded non-destructively:
  - Missing built-in symbols are merged in
  - Existing symbols are preserved
  - Custom symbols and categories are preserved
  - Duplicate non-space symbols are avoided per layer

---

## Symbol Palette System

- Active-layer scoped symbol palette
- Category-based palette organization
- Add, delete, and reorder categories
- Add, delete, and reorder symbols
- Edit symbol character
- Edit symbol display name
- Edit category color
- Edit per-symbol color
- Category color fallback for symbols without explicit colors
- Palette reset to layer-specific starter defaults
- Palette import:
  - `.json`
  - `.txt`
  - `.html`
- Palette export:
  - `.json`
  - `.txt`
  - `.html`
- Text and HTML palette exports include embedded machine-readable JSON payloads for round-tripping
- Color clipboard for copying/pasting symbol colors
- Active symbol indicators in desktop and mobile views
- Symbol grid tooltips

---

## Brush Mix

- Multi-symbol brush mode
- Toggle Brush Mix on/off
- Palette clicks add/remove symbols while Brush Mix is active
- Per-symbol weight inputs
- Weighted random symbol choice per painted cell
- Weighted random symbol choice per flood-filled cell
- Uniform random fallback when all mix weights are zero
- Brush Mix controls mirrored in:
  - Left desktop tool column
  - Right desktop Symbols panel
  - Mobile palette sheet
- Brush Mix clears/disables when changing layer, importing/resetting palette, opening project, or creating a new project

---

## Symbol Reference Modal

- Top-toolbar Symbols button opens the reference modal
- Examples tab with curated TERRASCII-oriented symbols
- Complete List tab with:
  - Printable ASCII characters 32-126
  - Additional non-ASCII curated symbols
- Per-symbol copy buttons
- Copy entire reference list
- Save reference list as `.txt`
- Save reference list as `.html`
- Complete List is derived from printable ASCII plus curated symbols, so it stays in sync as examples evolve

---

## Biome And Narrative Metadata

- Optional metadata syntax inside symbol names:

```text
Display Name | species:weight, species2
```

- Supports `:` or `=` for explicit weights
- Unweighted entries split remaining percentage evenly
- Duplicate metadata entries are combined by key
- Export-time parsing keeps editor state editable and lossless
- Manual JSON exports include:
  - Clean display `name`
  - Parsed `biomeData`
- Autosave preserves the original editable symbol name string
- Useful for AI narration, biome composition, ecosystem tags, and downstream game engines

Example:

```text
Mixed Pine & Hardwood | oak:30, pine:70
```

Exports as:

```json
{
  "name": "Mixed Pine & Hardwood",
  "biomeData": {
    "oak": 30,
    "pine": 70
  }
}
```

---

## Procedural Generation

### Weighted Random

- Fill selected tiles with weighted random symbols
- Percent weight inputs
- Live cumulative distribution display
- Coherence smoothing
- Overwrite/skip fill behavior
- Water-category symbols excluded from terrain weighted generation where appropriate

### Perlin Noise

- Perlin noise terrain generation
- Per-symbol enable/disable
- Dual-handle noise bands
- Scale control
- Octave control
- Seed control
- Coherence control
- Random bands action
- Reset bands action
- Overwrite/skip fill behavior

### Water

- Bodies of water pass
- Poisson-disk lake placement
- User-defined lake depth rings
- Perlin-wobbled organic lake shapes
- Rivers and streams pass
- Explicit source-to-destination route pairs
- Route pairs persist with project JSON/autosave
- Click-to-pick source/destination cells
- Editable river route style
- Editable stream route style
- Horizontal, vertical, corner, junction, and diagonal route symbols
- Route type color overrides for distinguishing rivers and streams even when symbols are reused
- Noise-guided river meander
- Short connected route pairs can be chained for cleaner meanders
- Destination pull
- Rivers stop at bodies of water
- Separate lake placement tracking

### Roads And Paths

- Road/path generation on the active layer
- Terrain, Underground, and Structures layer support
- Explicit source-to-destination route pairs
- Route pairs persist with project JSON/autosave
- Click-to-pick road endpoints
- Road and path route types
- Editable road route style
- Editable path route style
- Horizontal, vertical, corner, junction, and diagonal route symbols
- Route type color overrides for distinguishing roads and paths even when symbols are reused
- Noise-guided meander using river pathfinding foundation
- Short connected route pairs can be chained for cleaner meanders
- Composite refresh after generation

---

## Generation Presets

- Save named generation presets
- Presets capture generator tabs and settings
- Presets capture flowing water route styles
- Presets capture road/path route styles and route pairs
- Live route pairs persist as project generation state outside named presets
- Presets persist with project JSON
- Export presets as `.json`
- Import presets from `.json`
- Export human-readable preset summary as `.txt`

---

## Save, Export, And Import

### Project JSON

- Saves full layered project using the current v2 JSON schema
- Includes grid settings
- Includes layer metadata
- Includes all layer maps
- Includes palettes
- Includes cell color overrides
- Includes generation presets
- Manual downloads parse symbol biome metadata for engine-facing use
- Autosave uses lossless editor state

### Project TXT

- Exports non-empty maps as plain text
- Groups output by layer
- Suppresses empty layer maps
- Includes symbol summary

### Project HTML

- Exports non-empty maps as standalone HTML
- Groups output by layer
- Suppresses empty layer maps
- Preserves colors with inline spans
- Includes symbol summary

### Selected Map JSON

- Exports active layer and selected tile only
- Preserves project-style schema
- Keeps `gridCols` and `gridRows`
- Remaps selected tile to map index `0`
- Filters palette categories to used symbols
- Parses symbol biome metadata

### Selected Map TXT

- Exports selected active-layer tile as plain text
- Includes symbol summary

### Selected Map HTML

- Exports selected active-layer tile as standalone HTML
- Preserves colors with inline spans
- Includes symbol summary

### Project Open

- Opens v2 layered project JSON
- Opens legacy v1 project JSON
- Auto-migrates v1 projects to the three-layer system
- Merges missing current starter symbols into restored projects

---

## Autosave

- Debounced autosave after ordinary edits
- Immediate autosave after generation
- Pending autosave flush before refresh/navigation
- Uses `localStorage`
- Restores latest session on boot
- Preserves lossless editable project state
- Separates internal state from engine-facing manual JSON exports

---

## Keyboard And Pointer Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl + Z` | Undo |
| `Ctrl + Y` | Redo |
| `Ctrl + C` | Copy selection |
| `Ctrl + X` | Cut selection |
| `Ctrl + V` | Open Paste modal |
| `Ctrl + S` | Save project |
| `Ctrl/Command + Mouse Wheel` | Zoom canvas |
| `Shift + Left Drag` | Pan canvas |
| `Escape` | Clear selection or exit screenshot mode |

---

## Compatibility And Portability

- Modern browser support
- Works as a local file in most browsers
- No external runtime dependencies
- No package manager required
- No build step
- Project files are plain JSON
- Text exports are plain text
- HTML exports are standalone documents
