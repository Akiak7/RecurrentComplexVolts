# Recurrent Complex Volts

Recurrent Complex Volts is a staged Minecraft 1.20.1 Forge port of the
legacy Recurrent Complex mod. The current port focuses on preserving the old
Recurrent Complex structure ecosystem: bundled `.rcst` files, user structure
files, missing-mod tolerance, manual placement, worldgen compatibility, and the
first pieces of the creator workflow.

## Target

- Minecraft: `1.20.1`
- Forge: `47.4.10`
- Java: `17`
- Mod id: `reccomplex`

## Current 1.20.1 Scope

Implemented and actively tested:

- legacy `.rcst` loading from bundled resources and `config/reccomplex`;
- missing block fallback to `minecraft:air`;
- safe skipping/degrading of missing items, entities, and unsafe NBT;
- manual commands such as `/rc list`, `/rc check`, and `/rc place`;
- runtime compatibility audit commands with `/rc audit`;
- natural Overworld and Nether worldgen through the deferred compatibility bridge;
- legacy village, decoration/tree, sapling, maze, and script-generation subsets;
- command-side authoring with `/rc select`, `/rc export`, and selection edit commands;
- schematic interchange commands for listing, previewing, converting, and exporting
  Sponge `.schem` plus legacy `.schematic` files;
- first-pass authoring tool items: Block Selector, Floating Block Selector, and Inspector;
- client-side selection outline, floating selector preview, and operation
  preview/confirm/cancel for structure placement and clipboard paste;
- a first modern `/rc gui` browser for browsing structures/schematics,
  launching preview-first operations, making editable config copies, applying
  generation/expression/transformer/metadata presets, and running selection,
  clipboard, export, marker, and script-marker workflows from the Author tab.

Still in progress:

- the full old GUI/editor workflow beyond the current browser, preview launcher,
  generation panel, and Author tab;
- exact 1.12.2 transformer table, placer/dependency expression language, and
  variable-domain parity;
- true modern jigsaw-pool integration for village pieces;
- exact old planned/complemented chunk records for every generation type;
- old GUI-driven schematic conversion screens and rich schematic ghost previews;
- complete modded block/entity/item remapping beyond safe missing-content fallback.

## User Guide

This build is still command-first. `/rc gui` provides the first modern browser,
preview launcher, generation/expression/transformer panel, and Author tab,
while commands and tool items remain the authoritative way to inspect, place,
author, export, and debug structures.

Start in game with:

```text
/rc help
/rc status
```

`/rc help` lists short topic pages such as `/rc help authoring`,
`/rc help worldgen`, and `/rc help diagnostics`. `/rc status` gives a compact
alpha health summary with the loaded version, discoverable structure count,
structure folder, and major generation toggles.

### Permissions

Most read-only inspection commands are available to ordinary command users.
Commands that edit the world generally require permission level 2. Commands that
write structure files or metadata generally require permission level 4.

### Legacy Structure Ids

Legacy structure ids come from bundled `.rcst` files and user/exported files.
Examples:

- `SmallFortRuins`
- `StoneMaze`
- `nether/NetherThorn1`
- `village/VillageWoodMill`

Slash-qualified ids are supported by the main diagnostics and placement paths.
Exported structures are written under:

```text
config/reccomplex/structures/active/structures/<id>.rcst
```

### Compatibility vs Raw Placement

Most placement commands default to compatibility mode. Compatibility mode applies
the 1.20.1 adapter layer: old block metadata is flattened, missing mod blocks
become air, missing items/entities are skipped safely, RC internal placeholder
blocks use their placement semantics, block entities/entities are sanitized, and
supported transformers such as ruins/natural terrain are applied.

`raw` mode is a debug mode. It is useful when comparing captured block data, but
it is not the recommended way to place structures during normal testing.

For safer manual testing, use `/rc preview place <id>` first. It shows the
placement bounds and origin without changing blocks. `/rc confirm` commits the
pending preview and `/rc cancel` clears it. Command-placed structures, confirmed
previews, clipboard pastes, and marker-painting commands are kept in a small
per-player undo history; use `/rc undo` if a committed authoring operation lands
wrong.

### Basic Commands

- `/rc`
  - Confirms that the command bridge is loaded and points to help/status.
- `/rc help`
  - Lists in-game help topics.
- `/rc help <topic>`
  - Shows focused examples for `basics`, `library`, `place`, `authoring`,
    `worldgen`, `diagnostics`, or `config`.
- `/rc status`
  - Shows the alpha health summary: mod version, discoverable structures,
    user structure folder, and major generation bridge toggles.
- `/rc doctor`
  - Alias for `/rc status`.
- `/rc test`
  - Prints the loaded mod name/version and logs that the command ran.
- `/rc reload`
  - Reloads legacy structure/catalog caches. Use this after manually adding or
    editing files under `config/reccomplex/structures`.

### Legacy Library And Compatibility Audit

- `/rc list`
  - Lists the first page of loadable structures.
- `/rc list <filter>`
  - Lists the first page of structures whose ids match the filter text.
- `/rc list page <page>`
  - Browses all structures by page.
- `/rc list filter <text>`
  - Searches structure ids using explicit filter syntax.
- `/rc list filter <text> page <page>`
  - Searches and browses by page.
- `/rc info <id>`
  - Shows a human-friendly structure summary with dimensions, metadata,
    generation/script notes, compatibility warning count, and next commands.
- `/rc random`
  - Picks a random loadable structure id without placing it.
- `/rc random <filter>`
  - Picks a random matching structure id.
- `/rc last`
  - Shows the last structure id selected by `/rc info` or `/rc random`.
- `/rc check <id>`
  - Loads one structure, decodes its legacy data, and reports size, block
    entities, entities, placeholder semantics, script metadata, transformer
    notes, and conversion warnings without placing anything.
- `/rc audit`
  - Scans all loadable legacy structures and summarizes actionable conversion
    warnings.
- `/rc audit one <id>`
  - Audits one exact structure.
- `/rc audit filter <text>`
  - Audits matching structures.
- `/rc gui`
  - Opens the first modern RC browser. It lists structures and schematics,
    shows compact details, launches preview-first placement/import actions, and
    offers editable-copy/basic-metadata controls, generation preset/flag editing
    for exported config structures, plus an Author tab for selection, clipboard,
    export, marker, and script-marker workflows.
- `/rc copy <sourceId> <newId>`
  - Makes a safe editable config copy of any loadable structure, including
    bundled templates such as `SmallFortRuins` or slash ids such as
    `nether/NetherThorn1`. The output id must be new and path-safe.

Use audit commands when you want to understand compatibility gaps before trying
to place or generate a structure.

### Manual Structure Placement

- `/rc preview place <id>`
  - Shows a bounding preview near the player without changing blocks.
- `/rc preview place <id> at <x y z> [rotate <quarter_turns>] [mirror]`
  - Previews an exact transformed origin. Slash ids are supported.
- `/rc confirm`
  - Commits the current previewed structure placement or clipboard paste.
- `/rc cancel`
  - Clears the current preview without changing the world.
- `/rc undo`
  - Restores the latest undoable player-commanded edit in the current
    dimension. This covers `/rc confirm`, immediate `/rc place`, `/rc paste`,
    and marker-painting commands. Worldgen, exports, metadata edits, selection
    edits, preview creation, and cancel are not undoable.
- `/rc undo show`
  - Shows the latest undo entry and current stack count.
- `/rc undo clear`
  - Clears your in-memory undo history.
- `/rc place <id>`
  - Places a legacy structure near the player in compatibility mode.
- `/rc place <id> raw`
  - Places in raw debug mode.
- `/rc place <id> mirror`
  - Mirrors the structure.
- `/rc place <id> mirror raw`
  - Mirrors and uses raw debug mode.
- `/rc place <id> rotate <quarter_turns>`
  - Rotates by `0`, `1`, `2`, or `3` quarter turns.
- `/rc place <id> rotate <quarter_turns> raw`
  - Rotates and uses raw debug mode.
- `/rc place <id> rotate <quarter_turns> mirror`
  - Rotates, then mirrors.
- `/rc place <id> rotate <quarter_turns> mirror raw`
  - Rotates, mirrors, and uses raw debug mode.
- `/rc place <id> at <x y z>`
  - Places at an explicit origin.
- `/rc place <id> at <x y z> raw`
- `/rc place <id> at <x y z> mirror`
- `/rc place <id> at <x y z> rotate <quarter_turns>`
- `/rc place <id> at <x y z> rotate <quarter_turns> mirror`

The same options also work with slash ids such as:

```text
/rc place nether/NetherThorn1 raw
/rc place nether/NetherThorn1 at 100 64 100 rotate 1 mirror
```

### Worldgen Diagnostics

- `/rc worldgen status`
  - Shows natural worldgen config, whitelist behavior, queue caps, complement
    status, planning limits, and freeze diagnostics status.
- `/rc worldgen catalog`
  - Shows the currently supported natural worldgen catalog and category
    distribution.
- `/rc worldgen inspect <id>`
  - Shows natural-generation metadata and support status for one structure.
- `/rc worldgen queue`
  - Shows deferred placement queue, planned store, active store, ledger,
    complement, watchdog, and worker-planning diagnostics.
- `/rc worldgen recent`
  - Shows recent RC worldgen events such as queued, planned, resumed, placed,
    skipped, complement, and pressure statuses.

Worldgen uses a deferred compatibility bridge. The feature callback selects
legacy structures, but actual block mutation happens later on the server thread
when affected chunks are safe to touch.

### Sapling, Decoration, And Village Bridges

- `/rc sapling status`
  - Shows sapling bridge config.
- `/rc sapling catalog`
  - Shows supported sapling replacement entries.
- `/rc decoration status`
  - Shows decoration/tree bridge config.
- `/rc decoration catalog`
  - Shows supported decoration entries.
- `/rc decoration probe`
  - Probes decoration eligibility at the player's current position.
- `/rc decoration probe <x y z>`
  - Probes decoration eligibility at a specific position.
- `/rc vanilla status`
  - Shows the bounded legacy vanilla/village-piece bridge config.
- `/rc vanilla catalog`
  - Shows supported legacy village/vanilla hook entries.
- `/rc vanilla inspect <id>`
  - Shows parsed village metadata and limits for one structure.
- `/rc vanilla probe`
  - Probes village bridge eligibility near the player.
- `/rc vanilla probe <x y z>`
  - Probes village bridge eligibility near a specific position.

The village bridge is compatibility-focused. It honors parsed legacy weights and
per-village limits, but it is not full 1.20 jigsaw-pool integration yet.

### Script Block Diagnostics

- `/rc script show <x y z>`
  - Inspects one command or structure-spawner marker.
- `/rc scriptblocks nearby`
  - Lists modern RC script marker block entities within 16 blocks.
- `/rc scriptblocks nearby <radius>`
  - Lists marker block entities within `1..64` blocks.

This is useful when testing preserved script/action marker blocks and the
script-generation subset.

### Selection Commands

Selections are inclusive cuboids between `pos1` and `pos2`. The client overlay
shows a blue `pos1` marker, an orange `pos2` marker, and a cyan cuboid when the
selection is complete.

- `/rc select pos1`
  - Sets `pos1` to the player's current block position.
- `/rc select pos1 <x y z>`
  - Sets `pos1` explicitly.
- `/rc select pos2`
  - Sets `pos2` to the player's current block position.
- `/rc select pos2 <x y z>`
  - Sets `pos2` explicitly.
- `/rc select look pos1 [range]`
  - Sets `pos1` along your look ray. If no block is hit, it selects the floored
    air endpoint, matching the Floating Block Selector behavior.
- `/rc select look pos2 [range]`
  - Same as above for `pos2`. The default range is the normal floating-selector
    range; explicit ranges use the same safe bounds.
- `/rc select show`
  - Shows the current selection, dimensions, and volume.
- `/rc select clear`
  - Clears the current selection.
- `/rc select expand <amount>`
  - Expands all axes by the same amount.
- `/rc select expand <x> <y> <z>`
  - Expands each axis independently. Values are `0..64`.
- `/rc select shrink <amount>`
  - Shrinks all axes by the same amount.
- `/rc select shrink <x> <y> <z>`
  - Shrinks each axis independently. Values are `0..64`.
- `/rc select move <x> <y> <z>`
  - Moves the whole selection. Values are `-256..256`.
- `/rc select resize <west|east|down|up|north|south> <amount>`
  - Moves one face of the inclusive cuboid. Positive values grow that face
    outward; negative values shrink it inward. Resizes that would erase the
    selection are rejected.

Copy, marker, and export commands currently reject selections larger than
`262144` blocks.

The `/rc gui` Author tab is the same workflow with buttons, split into compact
`Select`, `Clipboard`, `Markers`, and `Script` pages. `Select` shows your current
`pos1`/`pos2`, dimensions, volume, coordinate fields, here/look buttons,
move controls, and one-face resize. `Clipboard` handles copy, paste preview,
`.rcst` export, and `.schem` export. `Markers` exposes common marker fills.
`Script` creates and edits command markers and simple child-structure spawners.
Selector items are still the fastest way to pick corners in the world; the GUI
is the easier place to inspect and precisely adjust that selection.

### Clipboard Commands

- `/rc copy`
  - Captures the current selection into an in-memory player clipboard.
- `/rc preview paste`
  - Shows a pending clipboard paste preview at the player's current block
    position. Use `/rc confirm` to paste or `/rc cancel` to clear it.
- `/rc paste`
  - Pastes the clipboard at the player's current block position.
- `/rc paste raw`
  - Pastes in raw debug mode.
- `/rc paste mirror`
  - Pastes mirrored.
- `/rc paste rotate <quarter_turns>`
  - Pastes rotated by `0..3` quarter turns.
- `/rc paste rotate <quarter_turns> mirror`
  - Pastes rotated and mirrored.
- `/rc paste <x y z>`
  - Pastes at an explicit origin.
- `/rc paste <x y z> raw`
- `/rc paste <x y z> mirror`
- `/rc paste <x y z> rotate <quarter_turns>`
- `/rc paste <x y z> rotate <quarter_turns> mirror`

The clipboard is temporary and is not written to disk. Use `/rc export`
when you want a persistent `.rcst`.

Clipboard paste is undoable after it is committed with `/rc paste` or
`/rc confirm`. The undo stack is intentionally in-memory and is cleared when you
log out or restart.

### Marker Painting Commands

Marker commands fill or smart-mark the current selection with RC internal marker
blocks. These are intended for authoring structures that blend with terrain or
preserve world space when placed.

- `/rc mark negative_space`
  - Fills the selection with `generic_space` metadata `0`. During compatibility
    placement this preserves existing world blocks.
- `/rc mark natural_air`
  - Fills the selection with `generic_space` metadata `1`. During compatibility
    placement this clears to air.
- `/rc mark natural_floor`
  - Fills the selection with `generic_solid` metadata `0`. During compatibility
    placement this preserves solid terrain and fills empty/replaceable terrain
    conservatively.
- `/rc mark barrier`
  - Fills the selection with `generic_solid` metadata `1`, the legacy barrier
    placeholder.
- `/rc mark smart_floor`
  - Adds natural-floor markers around detected solid surfaces.
- `/rc mark smart_floor <expansion>`
  - Uses floor expansion `0..2`; default is `1`.
- `/rc mark smart_space`
  - Adds negative-space and natural-air markers using default heuristics.
- `/rc mark smart_space <distance_to_floor>`
  - Uses distance `1..4`; default is `3`.
- `/rc mark smart_space <distance_to_floor> <max_closed_sides>`
  - Uses max closed sides `3..5`; default is `3`.
- `/rc mark smart`
  - Runs smart floor and smart space marking together.
- `/rc mark smart <floor_expansion>`
- `/rc mark smart <floor_expansion> <space_distance_to_floor>`
- `/rc mark smart <floor_expansion> <space_distance_to_floor> <space_max_closed_sides>`

Smart marking is a helper, not a full replacement for the old GUI editor. Always
inspect and test the exported result. Marker painting is undoable with `/rc undo`
as long as you remain in the same dimension and the affected chunks are loaded.

### Script Marker Authoring

The current build can author the RC script/action marker subset that already
runs in the 1.20.1 port: weighted command scripts and simple one-child
`strucGen` structure spawners. These marker edits are world edits, so they are
undoable with `/rc undo`.

- `/rc script show <x y z>`
  - Inspects one marker block.
- `/rc script command set <x y z> <command>`
  - Creates or replaces a `weighted_command_block` command marker.
- `/rc script command add <x y z> <weight> <command>`
  - Adds a weighted command entry.
- `/rc script command clear <x y z>`
  - Clears command entries while keeping the command marker block.
- `/rc script struc set <x y z> <childId>`
  - Creates or replaces a `spawn_script` marker using simple `strucGen` mode and
    one explicit child structure id.
- `/rc script struc shift <x y z> <shiftX> <shiftY> <shiftZ>`
- `/rc script struc front <x y z> <north|east|south|west>`
- `/rc script struc transform <x y z> <rotate0-3> <mirror true|false>`
  - Edit the child spawner anchor, front direction, rotation, and mirror.
- `/rc script trigger spawn <x y z> <true|false>`
- `/rc script trigger redstone <x y z> <true|false>`
  - Toggle placement-time and redstone trigger behavior.

Targets must be loaded and must be air or an existing RC script marker, so these
commands do not accidentally replace ordinary build blocks. The `/rc gui` Author
tab includes a `Script` page with Here/Look target helpers and the same command
and structure-spawner controls. Full `mazeGen` authoring, weighted child
structure lists, holder editing, and the old script GUI remain deferred.

### Exporting Structures

- `/rc export <id>`
  - Captures the current selection and writes an `.rcst` file under
    `config/reccomplex/structures/active/structures`.

Export ids may use slash paths, for example:

```text
/rc export my_pack/TestHouse
```

Exported files use the legacy `.rcst` container with modern-compatible block
mapping data. They are intended to load correctly in this 1.20.1 port; backward
loading in the old 1.12.2 mod is not guaranteed.

### Schematic Interchange

Schematics are interchange files, not the primary RC structure format. Put
Sponge `.schem` files or legacy MCEdit/Schematica `.schematic` files under:

```text
config/reccomplex/schematics
```

Then use:

- `/rc schem list`
  - Lists schematic names under the schematic folder.
- `/rc schem list <filter>`
  - Lists matching schematic names.
- `/rc schem preview <name>`
  - Loads the schematic through the RC compatibility adapter and creates a
    pending preview near the player. Use `/rc confirm`, `/rc cancel`, and
    `/rc undo` exactly like structure placement.
- `/rc schem preview <name> at <x y z> [rotate <0-3>] [mirror]`
  - Previews an exact transformed schematic import.
- `/rc schem convert <name> <id>`
  - Converts a schematic into
    `config/reccomplex/structures/active/structures/<id>.rcst`, then reloads the
    structure catalogs.
- `/rc schem export <name>`
  - Exports the current selection as a Sponge `.schem` file under the schematic
    folder.

The long `/rc schematic ...` spelling is also available. Sponge `.schem` import
preserves modern blockstate properties where possible. Legacy `.schematic`
import reads `Materials=Alpha` numeric block/data arrays and optional
Schematica mappings. Missing schematic blocks degrade to air with conversion
notes, just like missing content in `.rcst` structures.

### First Structure Workflow

For a tiny command-side authoring test:

```text
/give @p reccomplex:block_selector
/give @p reccomplex:block_selector_floating
/rc help authoring
```

Then:

- use Block Selector on one corner to set `pos1`;
- crouch-use Block Selector on the opposite corner to set `pos2`;
- run `/rc select show` to confirm the inclusive cuboid;
- optionally open `/rc gui`, switch to `Author`, and use its copy/export/marker
  buttons instead of typing the matching commands;
- run `/rc export TestExport`;
- run `/rc check TestExport` or `/rc audit one TestExport`;
- run `/rc preview place TestExport`, then `/rc confirm` if the box is correct.

Optional worldgen authoring smoke:

```text
/rc gen preset surface TestExport frequent
/rc gen validate TestExport
/rc reload
/rc worldgen inspect TestExport
```

Optional sapling authoring smoke:

```text
/rc gen preset sapling TestTree oak single
/rc gen shift TestTree 0 0 0
/rc gen validate TestTree
/rc reload
```

### Metadata And Generation Editing

`/rc gen` is the friendly generation-authoring surface for exported structures.
It writes legacy-compatible `generationInfos` into `structure.json`, so newly
exported structures use the same `.rcst` compatibility pipeline as old bundled
structures.

The `/rc gui` browser exposes the same safe editing path for selected exported
structures. Its generation editor is split into `Nat`, `Trig`, `Village`,
`Expr`, `Xform`, and `Meta` pages for natural fields, sapling/decoration
trigger fields, village-piece fields, dependency/biome/dimension expressions,
transformer presets, and basic metadata/copy actions. Bundled structures stay
read-only in the GUI; use Meta -> Make edit or `/rc copy <sourceId> <newId>`
before editing.

GUI text fields are explicit-apply fields. Typing a value does not mutate the
structure until the matching button is pressed, and invalid numbers or ids are
rejected locally and server-side before metadata changes.

- `/rc gen show <id>`
  - Shows a compact generation metadata summary for any loadable structure.
- `/rc gen validate <id>`
  - Checks whether one structure is loadable, has supported natural generation
    metadata, has positive weight and dimension eligibility, is allowed by the
    current worldgen config, has a satisfied dependency expression, and has
    obvious compatibility warnings.
- `/rc gen preset surface <id> [category]`
  - Writes a conservative Overworld surface natural-generation preset.
- `/rc gen preset underground <id> [category]`
  - Writes the closest currently supported buried/underground-style natural
    preset.
- `/rc gen preset nether_surface <id> [category]`
  - Writes a Nether natural-generation preset using the supported Nether
    surface placer.
- `/rc gen preset sapling <id> <oak|spruce|birch|jungle|acacia|dark_oak> [single|2x2]`
  - Writes a sapling replacement preset using one sapling at local `0 0 0`, or
    a 2x2 sapling pattern covering local `0..1 0 0..1`.
- `/rc gen preset decoration <tree|big_mushroom|cactus|desert_well|fossil> <id>`
  - Writes a vanilla decoration replacement preset using the current supported
    surface placement metadata.
- `/rc gen preset village <id> [any|plains|desert|savanna|taiga|snowy]`
  - Writes a bounded village-piece preset for the current legacy vanilla bridge.
    Defaults are weight `1.0`, front `north`, shift `0 0 0`, and cap `1`.
- `/rc gen natural category <id> <category>`
  - Updates only the natural generation category.
- `/rc gen natural weight <id> <weight>`
  - Updates only the natural generation weight.
- `/rc gen natural baseline <id> <y>`
  - Updates only the natural baseline Y.
- `/rc gen natural dimension <id> <overworld|nether|end|all>`
  - Updates only the natural dimension preset.
- `/rc gen sapling weight <id> <weight>`
  - Updates a sapling generation entry's weight while preserving its sapling
    type, pattern, and shift.
- `/rc gen decoration weight <id> <weight>`
  - Updates a decoration generation entry's weight while preserving its
    decoration type and shift.
- `/rc gen village front <id> <north|east|south|west>`
  - Sets the legacy village front direction used by the bridge.
- `/rc gen village shift <id> <x> <y> <z>`
  - Sets the village-piece spawn shift relative to the placement anchor.
- `/rc gen village weight <id> <weight>`
  - Sets the legacy village generation weight before the global village base
    weight is applied.
- `/rc gen village cap <id> <max_per_village>`
  - Sets a simple per-village cap for this custom piece.
- `/rc gen village biome <id> <any|plains|desert|savanna|taiga|snowy>`
  - Updates only the village biome preset expression.
- `/rc gen shift <id> <x> <y> <z>`
  - Sets the sapling/decoration spawn shift, useful when the trigger block
    should not be at the exported structure's lower corner.
- `/rc gen clear <id>`
  - Removes generation metadata from an exported structure.
- `/rc gen expr show <id>`
  - Shows dependency, natural biome/dimension, decoration biome/dimension, and
    village biome expression summaries.
- `/rc gen expr dependency <id> <expression>`
  - Sets the top-level dependency expression used by generation catalogs.
    Manual placement and read-only browsing still work when this expression is
    false.
- `/rc gen expr natural biome clear <id>`
  - Clears natural biome rules.
- `/rc gen expr natural biome allow <id> <expression>`
  - Adds a positive natural biome rule, such as `$FOREST` or `type=desert`.
- `/rc gen expr natural biome deny <id> <expression>`
  - Adds a negative natural biome rule.
- `/rc gen expr natural biome weight <id> <weight> <expression>`
  - Adds or updates a weighted natural biome rule.
- `/rc gen expr natural dimension preset <id> <overworld|nether|end|all>`
  - Replaces natural dimension rules with a supported preset.
- `/rc gen expr natural dimension clear <id>`
  - Clears custom natural dimension rules.
- `/rc gen expr natural dimension add <id> <weight> <expression>`
  - Adds a weighted natural dimension expression, such as `$NETHER` or
    `minecraft:overworld`.
- `/rc gen expr decoration biome clear|allow|deny|weight ...`
  - Edits the same kind of biome rules on the decoration generation entry.
- `/rc gen expr decoration dimension preset|clear|add ...`
  - Edits the same kind of dimension rules on the decoration generation entry.
- `/rc gen expr village biome <id> <expression>`
  - Sets the custom village biome expression directly when the preset command is
    not enough.
- `/rc transform show <id>`
  - Shows whether the structure has supported blend, natural-air, preserve-air,
    and ruins transformer metadata.
- `/rc transform preset blend <id>`
  - Adds terrain-blending metadata for `natural_floor` marker blocks.
- `/rc transform preset natural_air <id>`
  - Adds natural-air cleanup metadata for `natural_air` marker blocks.
- `/rc transform preset preserve_air <id>`
  - Preserves ordinary exported air cells instead of carving them into the
    target world.
- `/rc transform preset ruins <id> [light|medium|heavy]`
  - Adds a conservative ruins decay preset.
- `/rc transform clear <id> <blend|natural_air|preserve_air|ruins|all_supported>`
  - Removes supported transformer metadata while preserving unknown old
    transformer data.

Categories are `decoration`, `frequent`, `adventure`, `rare`, and `ultrarare`.
Dependency expressions support a practical modern subset: blank expressions,
loaded mod ids, `mod:<id>`, `$<id>`, `!`, `&`, `|`, and parentheses. Unsupported
old registry/structure dependency tokens are reported by `/rc gen expr show` and
`/rc gen validate`, and evaluate false for generation.

Preset mutation currently works on exported config structures only. Bundled
structures are read-only through this command bridge, so export a copy before
editing. The current edit commands use simple non-slash ids; use an id such as
`TestHouse` rather than `my_pack/TestHouse` if you plan to edit metadata through
commands.

Village-piece authoring uses the current bounded compatibility bridge. It is
useful for custom pieces, but it is not true 1.20 jigsaw-pool injection and does
not provide road attachment editing yet.

`/rc metadata` and `/rc meta` remain available as lower-level metadata editing
commands.

Inspection:

- `/rc metadata show <id>`
- `/rc meta show <id>`

Basic metadata:

- `/rc metadata set authors <id> <value>`
- `/rc metadata set comment <id> <value>`
- `/rc metadata set weblink <id> <value>`
- `/rc metadata set rotatable <id> <true|false>`
- `/rc metadata set mirrorable <id> <true|false>`
- `/rc metadata set blocking <id> <true|false>`

Natural generation metadata:

- `/rc metadata generation clear <id>`
  - Removes generation metadata.
- `/rc metadata generation natural_surface <id>`
  - Creates simple Overworld surface natural-generation metadata in the
    `decoration` category.
- `/rc metadata generation natural_surface <id> <category>`
  - Uses category `decoration`, `frequent`, `adventure`, `rare`, or
    `ultrarare`.
- `/rc metadata generation weight <id> <value>`
- `/rc metadata generation category <id> <category>`
- `/rc metadata generation baseline <id> <y>`

Biome rules:

- `/rc metadata generation biome clear <id>`
- `/rc metadata generation biome allow <id> <expression>`
- `/rc metadata generation biome deny <id> <expression>`
- `/rc metadata generation biome weight <id> <value> <expression>`

Dimension rules:

- `/rc metadata generation dimension preset <id> <overworld|nether|end|all>`
- `/rc metadata generation dimension clear <id>`
- `/rc metadata generation dimension add <id> <weight> <expression>`

The expression language is the currently supported legacy expression subset. Use
`/rc worldgen inspect <id>` and `/rc audit one <id>` after editing to
confirm the structure is eligible and compatible.

### Authoring Tool Items

The current command-backed authoring workflow has three item tools:

- `reccomplex:block_selector`
  - Use on a block to set `pos1`.
  - Crouch-use on a block to set `pos2`.
- `reccomplex:block_selector_floating`
  - Selects a point along the look vector, which is useful for air corners.
  - Crouch-use sets `pos2`; normal use sets `pos1`.
  - Ctrl-scroll while holding the item adjusts its range from `0` to `40`
    blocks. The default is `2`.
- `reccomplex:inspector`
  - Read-only block/state/block-entity inspection.
  - Reports whether a block is an RC internal placeholder/action marker.

Useful gives:

```text
/give @p reccomplex:block_selector
/give @p reccomplex:block_selector_floating
/give @p reccomplex:inspector
```

### Config

Forge writes the common config to:

```text
config/reccomplex-common.toml
```

Important groups:

- `legacyWorldgen`
  - Natural Overworld/Nether worldgen bridge, deferred queue limits, planned
    storage, chunk complements, concurrent planning, and freeze diagnostics.
- `legacySaplings`
  - Sapling replacement bridge.
- `legacyDecorations`
  - Tree, mushroom, cactus, desert well, and fossil decoration bridge.
- `legacyVanilla`
  - Bounded village/vanilla-piece bridge.

Useful diagnostics after changing config:

```text
/rc worldgen status
/rc sapling status
/rc decoration status
/rc vanilla status
```

## Structure Compatibility

Old 1.12.2 Recurrent Complex `.rcst` files are treated as user data. The port
does not replace the legacy format with a new default format. Instead, legacy
files are adapted at load/placement time.

Compatibility policy:

- preserve namespaced ids where possible;
- convert common old metadata/blockstate forms to modern 1.20.1 blockstates;
- replace unknown or missing blocks with air;
- skip unknown or missing items/entities safely;
- preserve block entity NBT only where the modern target is known and safe;
- log conversion gaps clearly through commands, audit output, and `latest.log`.

## Early-Access Notes

Worldgen currently uses a deferred compatibility bridge so legacy structures are
selected during worldgen but placed on the server thread when affected chunks are
safe to touch. This is intentionally conservative and avoids chunk-worker world
mutation. Under very fast creative flight, distant decoration catch-up may be
dropped as an alpha stability compromise.

Known limitations:

- the current GUI is a modern browser/editor front-end, not the full old 1.12.2
  editor stack;
- script authoring covers command markers and simple one-child structure
  spawners, while `mazeGen`, holder scripts, and weighted child-list editing are
  still deferred;
- village pieces use the bounded compatibility bridge, not true modern jigsaw
  pool injection;
- missing mod content degrades safely, usually to air, rather than being
  automatically remapped to unrelated vanilla blocks;
- some far-away decoration catch-up is intentionally ephemeral under fast-flight
  pressure.

When reporting bugs, include `logs/latest.log`, any crash report, the
world seed/dimension/position if relevant, the exact `/rc` command or GUI page
used, the structure/schematic id, and a screenshot for visible placement or GUI
issues.
