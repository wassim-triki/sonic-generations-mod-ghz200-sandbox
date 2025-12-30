# GHZ Act 2 (ghz200) - Sandbox Stage

Replaces Green Hill Zone Act 2 (Modern) with a custom sandbox terrain (free roam playground).
Currently includes: custom terrain + custom collision + custom spawn + GHZ skybox.

## Install (HedgeModManager)
1) Put the mod folder (or .zip) in your Mods directory.
2) Enable it in HedgeModManager.
3) Click Save and Play.

## What this mod changes
- Stage slot: `ghz200` (Green Hill Zone Act 2 - Modern)
- Terrain/resources: `disk/bb/Packed/ghz200/`
- Stage config + set data (spawn/collision refs): `disk/bb/#ghz200.ar.00` + `disk/bb/#ghz200.arl`

## Dev notes (pipeline)
- Visual terrain: export `map.fbx` → Hedgehog Converter → `Packed/ghz200/`
- Collision: export `collision.fbx` → Havok Converter → `collision.phy.hkx`
  - `Terrain.stg.xml` uses `<RigidBodyContainer>collision</RigidBodyContainer>`
- Set objects: edited in SonicGLvl (currently only `SonicSpawn`)
- Skybox: copied into `ghz200.ar.00` (`ghz_sky_000`, `ghz_sky_001` + materials/textures)

## Credits
- SEGA / Sonic Team (original game)
- Tools: HedgeModManager, Hedgehog Converter, Havok Converter, SonicGLvl, HedgeArcPack/SkythTools

## Changelog
- 0.1.0: Project skeleton created
- 0.2.0: Custom terrain + collision + spawn + GHZ skybox
