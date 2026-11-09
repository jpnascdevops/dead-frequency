# Dead Frequency architecture — provisional

**Status: REPORTED BY KIMI, NOT VERIFIED AGAINST SOURCE.** Update after actual project export and first successful local build.

## Reported stack
- Three.js / WebGL; Vite bundler; frontend only.
- No backend, database or authentication.
- localStorage reportedly stores audio and sensitivity preferences.
- 60 Hz fixed timestep with accumulated simulation steps capped at 5/frame.

## Reported modules
- `scene.js`: scene composition, rendering, game state, hitscan, interactions.
- `game/config.js`: tuneable values.
- `game/level.js`: 27×15 ASCII grid at 2m/cell, geometry, collision, interactions, BFS and line of sight.
- `game/player.js`: movement, flashlight, health, sprint, crouch.
- `game/weapon.js`: pistol, ammo, recoil, reloading.
- `game/enemies.js`: patrol/chase/dead, detection, navigation, attacks.
- `game/effects.js`: pooled blood/impact particles.
- `game/audio.js`: procedural Web Audio.
- `game/input.js`: keyboard/mouse and pointer lock.
- `game/ui.js` and `index.html`: UI and game states.

## Reported validation
Kimi reported 26 checks passing and no initialization errors in Chromium/SwiftShader. It reported ~38,720 triangles and 111 draw calls with four infected, including ~8,585-triangle infected models produced through Tripo. These are **unverified**, and SwiftShader cannot certify Intel UHD 620 performance.

## After import, required inventory
1. Inspect `package.json`, scripts, dependency lockfile, source paths and licenses.
2. Run clean install, build, tests and basic gameplay smoke test.
3. Identify asset sources/usage rights and source-of-truth game configuration.
4. Capture actual renderer settings and frame-time counters.
5. Diagram the scene and state transitions from real code.
6. Measure runtime on ThinkPad T480 with UHD 620 at 1280×720.
7. Update this document with verified observations and explicit commit SHA.

## Orchestrator boundary
Agent tools must live separately under `tools/agent_orchestrator/` and never alter game runtime unless assigned. CI should not run subscription-authenticated LLMs. Use deterministic builds/tests in CI if available.
