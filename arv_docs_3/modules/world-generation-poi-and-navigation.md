# Module Contract: World Generation - POI and Navigation Contracts

## Responsibility
This component defines POI selection/spawn contracts and chunk-local navigation outputs consumed by enemy locomotion.

## POI contracts
- `POIConfig.POI_TABLE` is authoritative weighted POI definition set, including type, footprint, road distance, loot, and enemy config. (world/poi_config.gd:4)
- `POISpawner.pick_poi() -> Dictionary|{}` filters missing gridmap scenes and performs weighted random selection. (world/poi_spawner.gd:8, world/poi_spawner.gd:14, world/poi_spawner.gd:22)
- `spawn_building` branches by POI type:
  - `gridmap` loads packed scene
  - `procedural` creates node with `building_generator.gd` and applies room-range config
  (world/poi_spawner.gd:37, world/poi_spawner.gd:107, world/poi_spawner.gd:121)
- `spawn_loot` and `spawn_enemies` honor POI count/radius/table configs and attach spawned nodes under chunk parent. (world/poi_spawner.gd:49, world/poi_spawner.gd:84)

## Navigation output contracts
- `ChunkGenerator._build_navigation_region()` emits `ChunkNavigationRegion` with road-following triangles over sampled Bezier segments. (world/chunk_generator.gd:334, world/chunk_generator.gd:377)
- Monster navigation consumer contract expects helper methods for nav-ready gating and direction resolution:
  - `_can_use_navigation()`
  - `_get_navigation_direction(destination)`
  (enemies/monster.gd:285, enemies/monster.gd:314)
- Runtime fallback contract: if nav is not ready/usable, monster uses direct steering and stuck recovery guards. (enemies/monster.gd:318, enemies/monster.gd:334)

## Failure surface
- Missing POI scenes are warned once and skipped, reducing content density without hard crash. (world/poi_spawner.gd:178)
- Navigation region generation can be skipped when generated vertex set is too small. (world/chunk_generator.gd:365)

## Source files used
- world/poi_config.gd
- world/poi_spawner.gd
- world/chunk_generator.gd
- enemies/monster.gd
