# Module: World Generation

## 1. Responsibility
- Stream and reclaim world chunks around the player so the road remains continuous at runtime.
- Build per-chunk terrain, road mesh, collision, and chunk-local navigation surfaces.
- Place POI content from data configuration through weighted selection and spawn dispatch.

## 2. Boundaries and Dependencies
- In scope:
  - `WorldGenerator` chunk lifecycle orchestration.
  - `ChunkGenerator` geometry, POI placement attempt, and navigation strip generation.
  - `POISpawner` scene loading/cache and POI content spawning.
  - `POIConfig` content table schema.
  - Procedural building handoff to `BuildingGenerator`.
- Out of scope:
  - Enemy AI movement and combat decisions (consumers of generated nav data).
  - Player controls, RV driving, and inventory/crafting systems.
- Primary dependencies:
  - Godot runtime scene tree and `NavigationRegion3D`/`NavigationMesh`.
  - `enemies/zombie.tscn` as the default spawned enemy scene.

## 3. Entry Points and Public Surface
- `world/world_generator.gd`
  - `_ready()` initializes noise and startup buffers.
  - `_process(_delta)` drives add/remove streaming decisions.
  - `_spawn_next_chunk()` instantiates and threads next transform state.
- `world/chunk_generator.gd`
  - `generate_chunk(start_transform, next_turn_angle, shared_noise, shared_detail_noise, shared_poi_spawner) -> Transform3D` is the primary generation contract.
- `world/poi_spawner.gd`
  - `pick_poi() -> Dictionary`
  - `spawn_building(...) -> Node3D`
  - `spawn_loot(...) -> void`
  - `spawn_enemies(...) -> void`

## 4. Internal Structure
| Part | Role | Key Symbols | File |
|---|---|---|---|
| Stream Controller | Maintains active chunk window and handoff state | `active_chunks`, `next_transform`, `next_turn_angle` | `world/world_generator.gd` |
| Chunk Builder | Generates road/terrain/collision/nav for one chunk | `generate_chunk`, `_build_terrain_mesh`, `_build_road_mesh`, `_build_navigation_region` | `world/chunk_generator.gd` |
| POI Dispatcher | Filters weighted table entries and spawns content | `pick_poi`, `spawn_building`, `spawn_loot`, `spawn_enemies` | `world/poi_spawner.gd` |
| POI Schema | Data-only definition of POI variants | `POI_TABLE` | `world/poi_config.gd` |
| Procedural Building Adapter | Optional procedural POI generation | `generate` | `world/building/building_generator.gd` |

## 5. Data Contracts
- Stream state contract:
  - `active_chunks` entries are dictionaries with `node`, `start_z`, and `end_z` fields.
- Generation handoff contract:
  - `generate_chunk(...)` returns the end transform for the next chunk start.
- Navigation strip contract:
  - `_build_navigation_region()` creates a road-following `NavigationRegion3D` child.
  - If too few strip vertices are produced, region creation is skipped and consumers must fall back.
- POI config contract:
  - `POI_TABLE` entries define type, weight, footprint constraints, loot definitions, and enemy definitions.
- Spawner contract:
  - `pick_poi()` may return `{}` when valid weighted entries are unavailable.

## 6. Configuration Touchpoints
- Main scene entry in `project.godot` routes runtime through `world/test_world.tscn`.
- Stream window and road length assumptions are controlled in `world/world_generator.gd`.
- Terrain resolution, road width, and nav strip settings are controlled in `world/chunk_generator.gd`.
- POI distribution and scene paths are controlled in `world/poi_config.gd`.

## 7. Failure Modes and Safeguards
- Missing player reference pauses stream updates (`_process` early return).
- Missing/invalid POI scene paths are skipped with warn-once behavior.
- Empty or invalid POI weights yield an empty pick result and no POI spawn.
- Navigation strip generation may be skipped when geometry is insufficient.
- Road tilt is clamped to reduce undrivable chunk outcomes.

## 8. Related Design Docs
- `docs/design/world-generation-and-poi.md`

## 9. Source Files Used
- `world/world_generator.gd`
- `world/chunk_generator.gd`
- `world/poi_spawner.gd`
- `world/poi_config.gd`
- `world/building/building_generator.gd`
- `project.godot`

## 10. Completeness Notes
- This module doc is intentionally contract-focused; runtime walkthroughs, test matrices, and tuning tradeoffs are kept in the related design doc.
- Unknowns that need runtime profiling (chunk cost, nav bake latency, asset completeness ratio) remain in design-level notes.