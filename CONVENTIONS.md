# Conventions

Standards and patterns used throughout this campaign filesystem.

---

## Directory Structure

```
DND/
├── campaign/           # The living campaign — sessions, story arcs, party, world state
│   ├── sessions/       # What happened during play (canonical record)
│   ├── story/          # Quest arcs, plot threads, hooks
│   ├── party/          # Player characters
│   └── world-state/    # The evolving state of the world
│       ├── current.md  # Always reflects "right now"
│       └── snapshots/  # Frozen copies taken before each session
├── world/              # Setting & worldbuilding (the stage, not the play)
│   ├── npcs/           # Non-player characters
│   ├── locations/      # Places — cities, dungeons, regions, buildings
│   ├── factions/       # Organizations, guilds, power groups
│   └── lore/           # History, religion, cosmology, culture
├── mechanics/          # Game mechanics & crunch
│   ├── encounters/     # Combat encounters & stat blocks
│   ├── items/          # Magic items, loot tables
│   └── homebrew/       # Custom rules, monsters, spells, class features
├── assets/             # Media files
│   ├── maps/           # Digitized maps & dungeon layouts
│   ├── handouts/       # Player-facing documents
│   └── scans/          # Raw photoscans of paper notes & drawings
└── reference/          # Quick-ref sheets, cheat sheets, lookup tables
```

## Templates

Each content folder contains a `_template.md` file showing the expected structure for entries in that folder. To create a new entry, copy the template and fill it in.

All templates use **YAML frontmatter** for structured, searchable fields and **freeform markdown** below for narrative content. This hybrid approach keeps things human-readable while allowing programmatic queries across files.

## The `_ideas/` Convention

`_ideas/` directories are **sandboxes for quick capture** — jots, sparks, half-formed thoughts that aren't ready for a proper entry yet. They exist at key levels of the tree:

| Location | For |
|----------|-----|
| `DND/_ideas/` | Big picture — campaign direction, meta thoughts |
| `campaign/_ideas/` | Session hooks, plot twists, "what if" scenarios |
| `world/_ideas/` | NPC sketches, location sparks, faction concepts |
| `mechanics/_ideas/` | Encounter ideas, homebrew drafts, item concepts |

### Idea Lifecycle

- **raw** — just a jot, stream of consciousness
- **developing** — being fleshed out or discussed
- **promoted** — graduated to a proper file in its destination folder
- **discarded** — didn't pan out, kept for the record

Each idea file has a `promote_to` field in its frontmatter indicating where it should land when ready. The `_` prefix keeps these folders sorted to the top and visually distinct from finalized content.

## World State Workflow

The `campaign/world-state/` system tracks the evolving state of the game world:

1. **Before a session** — snapshot `current.md` into `snapshots/pre-session-XX.md`
2. **Run the session** — optionally record for later transcription
3. **After the session** — fill out the session log in `sessions/`
4. **Between sessions** — update `current.md` using the "World State Changes" section from the session log

`current.md` should always reflect the present state of the world. Snapshots preserve history.

## File Naming

- Templates: `_template.md` (prefixed with `_` to sort first)
- Sessions: `session-01.md`, `session-02.md`, etc.
- Snapshots: `pre-session-01.md`, `pre-session-02.md`, etc.
- Everything else: lowercase, hyphen-separated (e.g., `elara-brightwood.md`, `waterdeep.md`)
