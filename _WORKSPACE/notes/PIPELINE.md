# Pipeline (GHZ200 Sandbox Stage)

This repo replaces **Sonic Generations GHZ Act 2 (Modern)** (`ghz200`) with custom terrain.
Pipeline covers: terrain (visual), collision (physics), spawn/set data, and skybox.

---

## Project paths (conventions)

**Per-map workspace (source + exports)**

```
_WORKSPACE/
  blender/                      # .blend files (source)
  exports/
    maps/
      /<map_name>/
        map.fbx                 # visual export
        collision.fbx           # collision export
        collision.phy.hkx       # generated collision (Havok)
        _tex/                   # textures referenced by map.fbx (flat filenames)
```

**Release mod (what HedgeModManager loads)**

```
_RELEASE/
  mod.ini
  README.md
  CREDITS.md
  preview.png
  disk/
    bb/
      Packed/
        ghz200/
          Stage.pfd
          Stage-Add.pfd
          ghz200.ar.00
          ghz200.arl
      #ghz200.ar.00
      #ghz200.arl
```

Notes:
- `base_game_files/` contains locally extracted game files for convenience (gitignored).
- Avoid committing tools and extracted game archives.

---

## Tools used

- **Hedgehog Converter**: converts `map.fbx` into stage terrain/resources.
- **Havok Converter**: converts `collision.fbx` into `collision.phy.hkx`.
- **SonicGLvl**: edits stage data (`#ghz200`), set objects, spawn, collision refs, and sky materials.
- **HedgeModManager**: enables/testing the mod.

---

## 0) One-time setup

### SonicGLvl shaders (required once)

If SonicGLvl errors about missing shaders, copy these from `bb3.cpk` into:
`<SonicGLvl>/database/shaders/`

- `shader_r.ar.00`
- `shader_r_add.ar.00`
- `shader_r_add.ar.01`
- `shader_r_add.ar.02`

(These are editor-only; never ship them in the mod.)

---

## 1) Blender export rules (important)

### Export-safe material rule

For reliable texture detection:

- Use a simple **Principled BSDF** material with **Image Texture** nodes.
- Plug maps directly:
  - BaseColor → Base Color
  - Roughness (Non-Color) → Roughness
  - Normal (Non-Color) → Normal Map node → Normal

Avoid complex node groups for export (FBX/Assimp may ignore BaseColor).

### Texture path rule (stable workflow)

- Store textures in: `_WORKSPACE/exports/maps/<map_name>/_tex/`
- Export FBX with: **Path Mode = Strip Path**
- Result: FBX references textures by filename only (e.g., `grass_BaseColor.jpg`), and Hedgehog Converter resolves them via `_tex/`.

### Always apply transforms

Before exporting:
- `Ctrl + A` → Apply Rotation & Scale

---

## 2) Build terrain (visual)

### A) Export visual FBX

From Blender:

- Select all visible meshes you want in the stage
- Export to: `_WORKSPACE/exports/maps/<map_name>/map.fbx`
- FBX options:
  - Selected Objects ✅
  - Path Mode: Strip Path ✅

### B) Convert with Hedgehog Converter

Inputs:

- Model: `_WORKSPACE/exports/maps/<map_name>/map.fbx`
- Textures dir: `_WORKSPACE/exports/maps/<map_name>/_tex/`

Output:

- `_RELEASE/disk/bb/Packed/ghz200/`

Expected updated files:

- `Stage.pfd`
- `Stage-Add.pfd`
- `ghz200.ar.00`
- `ghz200.arl`

Sanity check:
- Confirm timestamps changed on the above files.

---

## 3) Build collision (physics)

### A) Export collision FBX

Recommended: collision is simplified geometry (avoid tiny rocks/particles for collision).

Export to: `_WORKSPACE/exports/maps/<map_name>/collision.fbx`

### B) Convert with Havok Converter

Input:
- `_WORKSPACE/exports/maps/<map_name>/collision.fbx`

Output (keep filename stable):
- `_WORKSPACE/exports/maps/<map_name>/collision.phy.hkx`

Rule:
- Keep the name `collision.phy.hkx` so XML stays unchanged.

---

## 4) Inject collision into stage data (`#ghz200`)

### A) Open stage in SonicGLvl

- File → Open Level
- Open: `_RELEASE/disk/bb/#ghz200.ar.00`

### B) Locate extracted stage folder

On disk, find the extracted stage folder by searching within the SonicGLvl folder for:
- `Terrain.stg.xml`

Open the folder that contains `Terrain.stg.xml`.

### C) Copy collision into extracted stage folder

Copy:
`_WORKSPACE/exports/maps/<map_name>/collision.phy.hkx`

Paste into the folder from step B, overwrite existing.

### D) Confirm collision reference (usually unchanged)

In `Terrain.stg.xml` confirm:

```xml
<RigidBodyContainer>collision</RigidBodyContainer>
```

### E) Save Stage Data (repack)

In SonicGLvl:
- File → Save Stage Data

Sanity check:
- Confirm timestamps changed on:
  - `_RELEASE/disk/bb/#ghz200.ar.00`
  - `_RELEASE/disk/bb/#ghz200.arl`

---

## 5) Set objects + spawn (SonicGLvl)

- Open: `_RELEASE/disk/bb/#ghz200.ar.00`
- Remove unwanted objects (optional for sandbox).
- Place `SonicSpawn` and set Active = true.
- File → Save Stage Data.

---

## 6) Skybox (GHZ default)

`Terrain.stg.xml` sky section expects:

```xml
<Sky>
  <Model>ghz_sky_000</Model>
  <Model>ghz_sky_001</Model>
</Sky>
```

Skybox assets must exist in the stage resources archive:
`_RELEASE/disk/bb/Packed/ghz200/ghz200.ar.00`

Minimum files used (example from GHZ):

- `ghz_sky_000.model`
- `ghz_sky_001.model`
- `ghz_sky_km1_sky01_Sky_d.material`
- `ghz_sky_km1_sun.material`
- `ghz_sky_km1_cloud02_dif.dds`
- `ghz_sky_km1_cloud03_dif.dds`
- `ghz_sky_km1_cloud04_dif.dds`

Tip:
- Use SonicGLvl Material Editor to verify each material points to the correct `.dds`.

---

## 7) Test loop

1) Enable mod in HedgeModManager → Save and Play
2) Load: Green Hill Zone Act 2 (Modern)
3) Verify:
- Spawn works (SonicSpawn)
- Collision matches terrain (no falling through / invisible old walls)
- Skybox renders correctly
- Textures render correctly

If "no change" happens:
- Verify timestamps updated in `_RELEASE/`
- Make sure HMM is pointing to the correct mod folder
- Temporarily disable other mods and re-test

---

## Common gotchas

- If BaseColor doesn’t show in converter logs: simplify material (export-safe Principled setup).
- Particle systems / instancing can explode polycount; reduce or bake carefully.
- Always regenerate collision after changing terrain shape.
- Avoid committing extracted base game files or proprietary textures.
