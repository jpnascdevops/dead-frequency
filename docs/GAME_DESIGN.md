# Dead Frequency — Game vision (draft v0.2)

**Genre:** single-player first-person zombie survival horror with semi-open world, exploration and vehicles.  
**Visual style:** VHS/PS1-inspired geometry and atmosphere with readable, semi-realistic environments and modern responsive controls.  
**Setting direction:** late 1990s; the prototype is Blackridge response facility. A possible future fictional region is Vale de Cedro. Setting is not finalized.

## Five design pillars
1. Travel itself is engaging: cars, routes, fuel and spontaneous risks.
2. Combat feels good: readable targets, rewarding feedback and fluid FPS controls.
3. Exploration rewards curiosity: meaningful locations, logical resources, environmental storytelling.
4. The world reacts: noise attracts infected, danger changes across regions, radio informs discovery.
5. Survival without bookkeeping overload: resources matter; avoid needless hunger/thirst micromanagement.

## Preferred direction
- Campaign + sandbox, with freedom to explore between story objectives.
- Balanced survival, not hardcore simulation or nonstop arcade action.
- Intermediate vehicle depth eventually: fuel, damage, tires, engine condition, storage, upgrades.
- Radio as a core discovery/story system (signals, clues, transmissions, emergent opportunities).
- Optional safehouses and garages, without mandatory freeform construction.
- Emphasis on low-end performance: T480/UHD 620, 720p, measurable 30 FPS minimum target.

## Reported current prototype
Kimi's reported `c9b460b` build: single Blackridge indoor 3D level with pistol, zombies, health, items, B-12 key and escape objective. Implemented in Three.js/Vite/WebGL, not Godot/Unity; source **not yet imported/verified**.

## Next planned game milestone (Kimi already working; do not duplicate until delivered)
Extend the facility escape into a small outdoor parking area with a drivable vehicle, low fuel, fuel-can pickup and simple road extraction. Keep compact (~150x150 m) and preserve existing FPS systems. Performance must be measured on the actual ThinkPad.

## Longer term
Roads, fuel stations, workshops, homes, a town center, industrial area, forest/farms and emergency radio stations; dynamic zombie activity and region-based loading; more vehicles and shelters. These are **directional ideas, not accepted implementation tasks**.

## Non-goals for early iterations
Multiplayer, full 2x2 km streaming world, complex vehicle simulation, crafting trees, hunger/thirst bars, detailed base-building, dozens of concurrent infected, photorealistic rendering.

## Product rules
- Source code and assets must be exportable, versioned and license-audited.
- Generated assets require provenance/license review before commercial use.
- Game-playtesting feedback must be collected separately from automated tests.
- Kimi's technical reports are claims to verify, not acceptance evidence.
