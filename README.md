# TERRASCII v3.0

TERRASCII is a free ASCII worldbuilding and tilemap editor for game designers, writers, solo developers, tabletop creators, and anyone who likes building places out of symbols.

It is designed for making maps that are both visual and useful: forests, rivers, roads, caves, towns, structures, regions, encounters, and layered worlds can all be sketched, edited, generated, saved, and exported from one focused tool. TERRASCII works especially well for narrative games, roguelikes, simulation projects, tabletop settings, interactive fiction, and AI-assisted world description.

At its heart, TERRASCII treats ASCII maps as more than decoration. A symbol can represent terrain, structure, depth, vegetation, biome type, road, stream, river, cave wall, settlement feature, or any other idea your project needs. The result is a map that can be read by a person, reused by a designer, and exported for downstream game or storytelling systems.

TERRASCII is free for anyone to use, share, and adapt under the project license.

## What You Can Make

- Regional overworld maps
- Forest, biome, and wilderness maps
- River, lake, stream, road, and path networks
- Caves, dungeons, tunnels, and underground spaces
- Towns, ruins, roads, buildings, and surface structures
- Layered maps with terrain, underground, and structures separated
- ASCII maps for games, tabletop campaigns, fiction, prototypes, and worldbuilding notes
- Exportable map data for engines, tools, or AI narration systems

## Feature Overview

### Single-File Mapping Tool

TERRASCII is built as a self-contained editor that can be used locally, shared easily, and kept with a project folder. It supports both desktop and mobile layouts, with a wide-screen interface for detailed editing and a touch-friendly interface for smaller devices.

### Layered World Editing

Maps are organized across three layers:

- Terrain for land, water, vegetation, elevation, and broad geography
- Underground for caves, tunnels, dungeons, interiors, and buried features
- Structures for roads, buildings, ruins, towns, and surface details

Layers can be shown, hidden, renamed, and edited independently. This makes it easier to build maps where the ground, hidden spaces, and constructed features all remain separate but still compose into one readable world.

### Symbol Palettes

TERRASCII includes expanded default symbol palettes for terrain, underground spaces, and structures. Symbols can be organized into categories, recolored, renamed, imported, exported, and customized for a specific project.

The Symbols panel is a major part of the desktop workspace, making palette selection and symbol management feel central rather than tucked away. A built-in ASCII and symbol reference gives users a place to browse useful characters without leaving the tool.

### Brush Mix

Brush Mix lets users paint with more than one symbol at a time. Each symbol in the mix can have its own weight, allowing natural variation when painting terrain, forests, ruins, rough ground, vegetation, or any repeated texture.

This is especially useful for maps that should feel organic rather than stamped from a single repeated character.

### Biome And Narrative Metadata

Symbols can carry richer meaning for narrative or game systems. A forest tile can represent a forest type, such as oak-hickory, northern hardwood, mixed pine and hardwood, or any custom ecosystem the creator defines.

This allows exported maps to support more descriptive narration. Instead of only saying what a tile is called, downstream systems can understand what kinds of natural elements belong there and describe the place in sensory, world-aware language.

### Procedural Generation

TERRASCII includes several generation tools for quickly creating or roughing in maps:

- Weighted random terrain generation
- Perlin-style natural terrain bands
- Lake generation with depth rings and organic shorelines
- River and stream generation
- Road and path generation
- Generation presets for reusable settings

Generated results remain editable, so users can combine procedural layout with hand-made map design.

### Rivers, Streams, Roads, And Paths

Rivers, streams, roads, and paths can be created from source-to-destination route pairs. Each route type can have its own symbols and colors for horizontal sections, vertical sections, corners, junctions, and diagonals.

For more natural meanders, shorter connected routes usually work better than one very long route. Chaining multiple smaller source-and-destination pairs gives the route more room to bend naturally while still guiding the overall course.

### Editing Tools

TERRASCII includes practical editing tools for map work:

- Paint, erase, fill, and selection modes
- Multiple brush sizes
- Copy, cut, paste, and placement options
- Undo and redo
- Per-cell color overrides
- Canvas zoom and panning
- Coordinate readouts for tile, cell, and world position
- Screenshot mode for clean map captures

### Save, Export, And Reuse

Projects can be saved and reopened later with their layers, palettes, colors, generation settings, route pairs, and map data intact.

Maps can also be exported in formats useful for different workflows, including project files, plain text, HTML, and selected-map exports. This makes TERRASCII useful both as a standalone mapping tool and as a companion for other creative or technical pipelines.

### Autosave

TERRASCII keeps local autosave state when the browser allows it, helping preserve work between sessions. Generation changes and route-pair edits are saved as part of the project state, so users can return to ongoing maps without rebuilding their generation setup from scratch.

## Who It Is For

TERRASCII is for:

- Game designers prototyping worlds and levels
- Writers mapping fictional places
- Tabletop creators preparing regions, dungeons, and settlements
- Roguelike and simulation developers
- Solo developers who want a lightweight mapping workflow
- AI-assisted game and narrative projects that benefit from structured map data
- Anyone who enjoys the clarity and charm of ASCII maps

## Design Philosophy

TERRASCII is meant to be portable, readable, playful, and practical. It keeps the immediacy of ASCII mapping while adding modern editing comforts: layers, palettes, generation, autosave, exports, symbol metadata, and responsive interfaces.

It is small enough to keep close, flexible enough to fit many projects, and open enough for others to use in whatever way helps them make worlds.

## License

MIT — see LICENSE for details.

