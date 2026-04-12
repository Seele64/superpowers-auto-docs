# Design: World Generation and POI

## 1. Why This Matters
- This partition keeps the road loop alive by continuously generating drivable chunks ahead and reclaiming chunks behind.
- It is also the main source of scavenging opportunities because POIs, loot, and enemy encounters are spawned during chunk generation.
- If this flow regresses, both traversal and resource progression degrade immediately.

## 2. Problem Statement
The runtime world loop must satisfy four constraints at the same time:
1. Stream forward with bounded memory growth.
2. Preserve drivability while generating terrain and road geometry dynamically.
3. Provide chunk-local navigation surfaces that are ready for AI consumption.
4. Place POI content from weighted data while tolerating missing resources.

## 3. Goals and Non-Goals
- Goals:
  - Maintain continuous chunk streaming around player position.
  - Keep road mesh, terrain blend, and nav strip generation aligned to the same curve model.
  - Keep POI placement data-driven through `POI_TABLE`.
  - Keep missing asset behavior non-fatal (skip + warn).
- Non-goals:
  - Full deterministic runs across all random decisions.
  - Full automation of world-stream quality checks in current state.

## 4. Options Considered
- Option A: Global pre-baked world with sparse runtime spawning.
  - Pros: stable nav and fewer runtime spikes.
  - Cons: poor replayability and large static content footprint.
- Option B: Fully procedural runtime chunking with local nav strips.
  - Pros: high replayability, bounded active scene complexity.
  - Cons: runtime generation cost and occasional fallback behavior.
- Chosen approach: Option B with guardrails (tilt clamp, skip-on-missing, nav fallback).

## 5. Constraints and Assumptions
- Chunk lifecycle is keyed to player Z progression.
- POI placement is best-effort and may skip a chunk entirely.
- Navigation region creation can be skipped in geometry edge cases, and AI must handle fallback movement.
- Noise seeds are fixed for terrain profile stability, but random POI/turn sampling remains nondeterministic.

## 6. Canonical Workflow
1. `WorldGenerator._ready()` initializes noise and pre-fills behind/current/ahead buffers.
2. `_spawn_next_chunk()` instantiates `ChunkGenerator` and hands off shared noise/spawner.
3. `generate_chunk(...)` builds terrain, road mesh, and per-chunk navigation strip.
4. If POI is selected and placement is valid, spawner creates building/loot/enemy content.
5. Ambient road zombies are spawned for baseline encounter density.
6. `WorldGenerator._process(_delta)` continues add/remove chunk management as player advances.

## 7. Interfaces and Contracts
- Primary generation contract:
  - `ChunkGenerator.generate_chunk(...) -> Transform3D` returns next-start transform.
- POI dispatch contract:
  - `pick_poi() -> Dictionary` may return `{}`.
  - `spawn_building`, `spawn_loot`, and `spawn_enemies` run as optional steps.
- Navigation handoff contract:
  - `_build_navigation_region()` attempts to attach `ChunkNavigationRegion` each chunk.
  - Consumers must support no-region fallback for edge cases.

## 8. Edge Cases and Failure Patterns
- Player reference missing: stream loop stalls until reference is valid.
- POI path missing: entry is skipped and warning is emitted once.
- Total POI weight invalid: pick returns empty and no POI is spawned.
- Navigation strip underflow: region creation exits early when vertex set is too small.
- Road tilt extremes: lateral elevation difference is clamped to maintain drivability.

## 9. Validation Checklist
- [ ] Confirm chunks continue spawning and old chunks are reclaimed while driving forward.
- [ ] Confirm generated chunks include terrain collision and road collision.
- [ ] Confirm chunk nodes contain `ChunkNavigationRegion` in normal cases.
- [ ] Confirm missing POI resources degrade to warnings instead of hard failure.
- [ ] Confirm zombie movement remains functional when nav data is missing (fallback behavior).
- [ ] Confirm procedural POI branch creates buildings with bounded room counts.

## 10. Related Modules
- `docs/modules/world-generation.md`

## 11. Source Files Used
- `world/world_generator.gd`
- `world/chunk_generator.gd`
- `world/poi_spawner.gd`
- `world/poi_config.gd`
- `world/building/building_generator.gd`
- `enemies/monster.gd`
- `enemies/zombie.tscn`
- `tests/test_monster_navigation.gd`

## 12. Completeness Notes
- This design doc keeps volatile workflow and validation detail for world generation.
- Unknowns still requiring runtime evidence: chunk-cost profile under load, nav-bake latency impact, and missing-asset ratio across all configured POIs.