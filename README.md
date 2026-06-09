# Recurrent Complex Volts

Recurrent Complex Volts brings classic Recurrent Complex structures and
worldgen to Minecraft 1.20.1 Forge. It loads old RC `.rcst` structure files,
generates bundled structures in new worlds, and gives players an in-game GUI for
browsing, previewing, placing, exporting, editing, and diagnosing structures.

The port is built around compatibility. Missing mod content degrades safely,
usually to air, instead of crashing a world. Old `.rcst` files remain the main
Recurrent Complex structure format.

Current public baseline: `0.4.2.0`.

## Supported Version

- Minecraft: `1.20.1`
- Forge: `47.4.10`
- Java: `17`
- Mod id: `reccomplex`

Install it like a normal Forge mod by putting the built jar in your `mods`
folder.

## Quick Start

In game, run:

```text
/rc gui
```

Then use the GUI:

1. Open `Structs`.
2. Search or pick a structure.
3. Click `Preview` to show the ghost preview.
4. Click `Confirm` to place it, or `Cancel` to discard the preview.
5. Click `Undo` if a confirmed placement needs to be rolled back.

For built-in help and diagnostics:

```text
/rc help
/rc status
```

For the detailed command map, see
[`docs/porting/command-reference.md`](docs/porting/command-reference.md).

## Main GUI Tabs

- `Structs`
  - Browse bundled and exported `.rcst` structures, inspect details, preview
    structures, confirm/cancel placements, undo, and pick random structures.
- `Schems`
  - Browse `.schem` and legacy `.schematic` files, preview schematic imports,
    and convert schematics into editable `.rcst` structures.
- `Auth`
  - Work with selections, clipboard copy/paste, `.rcst` export, `.schem`
    export, marker painting, and script marker authoring.
- `Balance`
  - Make RC worldgen rarer, commoner, disabled, or spaced out without opening
    the config file by hand.
- `Gen`
  - Edit one selected structure: make editable copies, set generation rules,
    tune natural/sapling/decoration/village metadata, edit supported
    transformers, repair missing blocks, and update basic metadata.

Most GUI buttons have hover tooltips. Text lines shortened with `...` also show
the full text on hover.

## Common GUI Workflows

### Try A Bundled Structure

1. Run `/rc gui`.
2. In `Structs`, select something like `SmallFortRuins`.
3. Click `Preview`.
4. If the ghost looks right, click `Confirm`.
5. Use `Undo` if you want to remove it again.

`Normal` placement is the recommended compatibility mode. `Raw` is a debug-style
direct copy mode and is not recommended for ordinary placement.

### Import A Schematic

Put Sponge `.schem` or legacy MCEdit/Schematica `.schematic` files under:

```text
config/reccomplex/schematics
```

Then:

1. Run `/rc gui`.
2. Open `Schems`.
3. Select the schematic.
4. Click `Preview`, then `Confirm` or `Cancel`.
5. Use `Convert` if you want to save it as an RC `.rcst` structure.

Schematic import uses the same compatibility resolver as `.rcst` structures
where possible, including old vanilla flattening, missing-block replacements,
and safe air fallback.

### Export A Custom Build

1. Use `reccomplex:block_selector` on one corner to set `pos1`.
2. Crouch-use it on the opposite corner to set `pos2`.
3. Use `reccomplex:block_selector_floating` when you need to select an air
   corner.
4. Run `/rc gui` and open `Auth`.
5. Use `Select` to inspect or adjust the inclusive cuboid.
6. Use `Clip` to copy, paste-preview, export `.rcst`, or export `.schem`.

Exported `.rcst` files are written under:

```text
config/reccomplex/structures/active/<id>.rcst
```

Run `/rc reload` or click `Refresh` after manually adding files.

### Make A Bundled Structure Editable

Bundled structures are read-only templates. To edit one:

1. Open `/rc gui`.
2. Select the bundled structure in `Structs`.
3. Open `Gen`.
4. Open `Meta`.
5. Use `Make edit` to create a config/user copy.
6. Edit the copied structure instead of the bundled original.

This protects bundled resources while still letting you customize them.

### Edit Generation, Rules, And Transformers

Open `/rc gui -> Gen` on an editable structure.

- `Nat`
  - Natural generation category, weight, baseline, dimension, and surface-style
    presets.
- `Trig`
  - Sapling and vanilla decoration replacement presets, weights, and trigger
    shifts.
- `Vil`
  - Bounded RC village-piece metadata such as biome preset, front direction,
    weight, cap, and shift.
- `Rules`
  - Simple biome and dimension allow/block rules. You can type values like
    `forest`, `plains`, `#minecraft:is_forest`, `overworld`, `nether`, `end`,
    or `all` without learning old RC expression syntax.
- `Xform`
  - Supported transformer presets and details for terrain blend, natural air,
    preserve air, and ruins.
- `Meta`
  - Make editable copies and edit authors, comment, weblink, and basic flags.
- `Fix`
  - Inspect missing blocks and choose replacement behavior.

GUI fields are explicit-apply fields. Typing a value does not change the
structure until you press the matching button.

### Fix Missing Blocks

Missing mod blocks become air by default. The `Gen -> Fix` page gives two
repair choices:

- `Set Global`
  - Saves a replacement rule in
    `config/reccomplex/missing-block-replacements.json`. This affects every
    structure with the same missing legacy block id.
- `Apply Here`
  - Rewrites only the selected editable `.rcst` structure so that one missing
    state becomes the typed modern blockstate. Other structures are unaffected.
- `Clear Global`
  - Removes the selected global replacement rule.

Examples of valid replacements:

```text
minecraft:calcite
minecraft:oak_log[axis=y]
```

Bundled structures must be copied with `Meta -> Make edit` before `Apply Here`
can rewrite them.

### Make Structures Rarer Or Commoner

Open `/rc gui -> Balance`.

- `0` disables a multiplier-based path.
- `0.5` means half as common.
- `1` means normal.
- `2` means twice as common.
- `rarity` is the old denominator: higher means rarer.

`Balance` covers natural structures, category multipliers, saplings,
decorations, villages, and optional spacing/anti-clumping for RC natural
structures.

## What The Mod Adds

- Classic RC `.rcst` structure loading for Minecraft 1.20.1 Forge.
- Natural Overworld and Nether structures through a deferred compatibility
  worldgen bridge.
- Bundled ruins, trees, dungeons, mazes, villages, Nether structures,
  decorations, sapling replacements, terrain blending, and script-generation
  subsets.
- GUI-first structure browsing, ghost preview, confirm/cancel, undo, schematic
  import/export, authoring, generation editing, transformer editing, script
  marker editing, missing-block repair, and worldgen balance controls.
- Missing-content tolerance for old structure packs from modded 1.12.2 worlds.
- Compatibility audits and diagnostics for large imported structure libraries.
- Vanilla locate support for aggregate RC natural structures:

```text
/locate structure reccomplex:natural_structure
```

Specific predictive RC locating is also available:

```text
/rc locate StonePlantMound
/rc locate StonePlantMound family
```

## Essential Commands

The GUI is the normal starting point. These commands are the useful backup and
diagnostic surface:

- `/rc gui`
  - Open the browser/editor GUI.
- `/rc help`
  - Show in-game help topics.
- `/rc status`
  - Show version, structure count, user folders, and major generation toggles.
- `/rc reload`
  - Reload structure and generation catalogs after manual file changes.
- `/rc worldgen balance`
  - Show the current frequency/balance settings and config path.
- `/rc worldgen status`
  - Show worldgen bridge, queue, spacing, and diagnostics state.
- `/rc missing dump [filter]`
  - Write a detailed missing-block report for old structure packs under
    `config/reccomplex/reports`.
- `/rc check <id>`
  - Inspect one structure without placing it.
- `/rc audit one <id>`
  - Audit one structure for compatibility issues.

For full syntax, see
[`docs/porting/command-reference.md`](docs/porting/command-reference.md).

## Authoring Tool Items

The current GUI-backed authoring workflow has three item tools:

- `reccomplex:block_selector`
  - Use on a block to set `pos1`.
  - Crouch-use on a block to set `pos2`.
- `reccomplex:block_selector_floating`
  - Selects along your look vector, including air endpoints.
  - Normal use sets `pos1`; crouch-use sets `pos2`.
  - Ctrl-scroll while holding the item adjusts range from `0` to `40` blocks.
- `reccomplex:inspector`
  - Read-only block/state/block-entity inspection.
  - Reports RC internal placeholders and script/action markers.

Useful gives:

```text
/give @p reccomplex:block_selector
/give @p reccomplex:block_selector_floating
/give @p reccomplex:inspector
```

## Structure Compatibility

Old 1.12.2 Recurrent Complex `.rcst` files are treated as user data. The port
does not replace the legacy format with a new default format. Instead, old data
is adapted at load, preview, placement, export, and generation time.

Compatibility policy:

- preserve namespaced ids where possible;
- convert common old metadata/blockstate forms to modern 1.20.1 blockstates;
- replace unknown or missing blocks with air unless a replacement is configured;
- skip unknown or missing items/entities safely;
- preserve block entity NBT only where the modern target is known and safe;
- report conversion gaps through GUI pages, commands, reports, and logs.

Missing block replacement rules are global unless you use `Apply Here` on an
editable structure.

## Config And Frequency

Forge writes the common config to:

```text
config/reccomplex-common.toml
```

The GUI `Balance` tab is the easiest way to change the major knobs. Commands and
manual config edits are still available for pack maintainers.

Important groups:

- `worldgen`
  - Natural generation, deferred placement limits, optional spacing, native
    locate support, complement behavior, and diagnostics.
- `saplings`
  - RC sapling replacement.
- `decorations`
  - Tree, mushroom, cactus, desert well, and fossil replacement.
- `villages`
  - Bounded RC village-piece bridge.

Older config section names are migrated automatically where possible.

## Runtime Notes And Known Limitations

Worldgen uses a deferred compatibility bridge. RC structures are selected during
worldgen, then placed later on the server thread when affected chunks are safe
to touch. This is intentional; it avoids unsafe chunk-worker world mutation while
letting old multi-chunk structures work in modern Minecraft.

Known limitations:

- the current GUI is a modern browser/editor front-end, not the full old 1.12.2
  editor stack;
- full old transformer table parity, exact expression parity, and some advanced
  legacy editor systems are still deferred;
- village pieces use the bounded compatibility bridge, not true modern jigsaw
  pool injection;
- script authoring covers command markers, simple child structure spawners, and
  existing RC structure-list spawners, while `mazeGen`, holder scripts, custom
  environment matchers, and embedded per-marker child-table editing remain
  deferred;
- missing mod content degrades safely, usually to air, rather than being
  automatically remapped to unrelated vanilla blocks;
- some far-away decoration catch-up is intentionally ephemeral under very fast
  creative flight pressure.

For release testing, use `./gradlew verifyReleaseJar` plus the checklist in
[`docs/porting/release-checklist.md`](docs/porting/release-checklist.md).

When reporting bugs, include `logs/latest.log`, any crash report, the world
seed/dimension/position if relevant, the GUI page or `/rc` command used, the
structure/schematic id, and a screenshot for visible placement or GUI issues.

## Building From Source

Most users do not need this section. It is for developers or pack maintainers
building the jar locally.

Use Java 17:

```sh
JAVA_HOME=/Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home ./gradlew build
```

The built mod jar is written under `build/libs/`.

For a development client:

```sh
JAVA_HOME=/Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home ./gradlew runClient
```

For a development server:

```sh
JAVA_HOME=/Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home ./gradlew runServer
```

The modern port compiles from `src/modern/java` and `src/modernTest/java`. The
legacy 1.12.2 source remains in `src/main/java` as reference material and is not
compiled by the modern source set.

Build target details:

- Minecraft: `1.20.1`
- Forge: `47.4.10`
- Java: `17`
- Gradle wrapper: `8.8`
