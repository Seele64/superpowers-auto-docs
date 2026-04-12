# World Generation and POI: Navigation and AI Coupling

## Scope
This document covers chunk-local navigation mesh generation and how monster movement consumes or falls back from that navigation data.

## Chunk navigation generation
- Each generated chunk builds a `NavigationMesh` strip through `_build_navigation_region`. (world/chunk_generator.gd:334)
- Navigation mesh agent parameters are configured at generation time (`max_slope`, `height`, `radius`). (world/chunk_generator.gd:336, world/chunk_generator.gd:337, world/chunk_generator.gd:338)
- Strip geometry follows Bezier-sampled road centerline with width derived from `ROAD_WIDTH * 0.9`. (world/chunk_generator.gd:342)
- Generated region is attached as `ChunkNavigationRegion`; generation is skipped when nav vertex count is too small. (world/chunk_generator.gd:365, world/chunk_generator.gd:377)

## Monster coupling behavior
- Zombie scene embeds `NavigationAgent3D`; monster resolves it at runtime and uses it for patrol/chase steering when map data is ready. (enemies/zombie.tscn:48, enemies/monster.gd:78, enemies/monster.gd:185, enemies/monster.gd:207)
- When navigation is unavailable, monster falls back to direct steering vectors. (enemies/monster.gd:314, enemies/monster.gd:318)
- Stuck watchdog tracks low progress and triggers bounded recovery with forced repath and cooldown. (enemies/monster.gd:334, enemies/monster.gd:350, enemies/monster.gd:355)

## POI and spawn coupling
- After chunk geometry build, POI pipeline runs in order: building, loot, enemies; then ambient road zombie spawn pass. (world/chunk_generator.gd:66, world/chunk_generator.gd:67, world/chunk_generator.gd:68, world/chunk_generator.gd:71, world/chunk_generator.gd:385)
- POI selection is weighted and filters missing gridmap scenes before sampling. (world/poi_spawner.gd:8, world/poi_spawner.gd:14)

## Source files used
- world/chunk_generator.gd
- world/poi_spawner.gd
- enemies/monster.gd
- enemies/zombie.tscn
