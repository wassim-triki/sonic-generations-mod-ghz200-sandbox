# GHZ200 Custom Stage Sandbox (Sonic Generations)

Development repository for a **stage replacement mod** that targets **GHZ Act 2 (Modern)** (`ghz200`).
Goal: iterate quickly on custom terrains (and later objects/props) with a repeatable pipeline.

If you’re looking for the playable build, see: `_RELEASE/`

---

## Repo layout

```
_WORKSPACE/                   # dev stuff (sources + exports)
  blender/                    # .blend source files
  exports/
    maps/
      /<map_name>/            # self-contained export per map experiment
        map.fbx
        collision.fbx
        collision.phy.hkx
        _tex/
  notes/
    PIPELINE.md               # the build + injection steps (source of truth)

_RELEASE/                     # what HedgeModManager loads (ship this)
base_game_files/              # local extracted game files (gitignored)
```

---

## Quick start (dev)

1) Pick a map folder:
   `_WORKSPACE/exports/maps/<map_name>/`

2) Export from Blender:
   - Visual: `map.fbx` (Path Mode: Strip Path)
   - Collision: `collision.fbx`
   - Textures in: `_tex/`

3) Run converters + inject collision/set data.

**Follow:** `_WORKSPACE/notes/PIPELINE.md`

---

## Version control notes

- Do **not** commit: tools, extracted base game archives, converter temp folders.
- Be careful with third‑party textures/models: only commit/ship assets that allow redistribution.
- Attribution for external assets belongs in:
  - `_RELEASE/CREDITS.md` (also OK to mirror in repo root if you want)

---

## License / legal

This is a fan-made mod project and is not affiliated with SEGA.
You must own Sonic Generations to use it. Please respect third-party asset licenses (see CREDITS.md).

---

## Credits

See `_RELEASE/CREDITS.md`.
