# World Generation and POI: Streaming and Geometry

## Scope
This document covers chunk streaming, chunk transform chaining, terrain/road mesh generation, and POI footprint terrain blending.

## Streaming behavior
- World streaming authority is `WorldGenerator` with `CHUNKS_AHEAD = 3` and `CHUNKS_BEHIND = 2`. (world/world_generator.gd:4, world/world_generator.gd:5)
- `active_chunks` stores per-chunk node and z-range metadata (`start_z`, `end_z`). (world/world_generator.gd:7)
- Startup pre-fills behind chunks, then current and ahead buffers. (world/world_generator.gd:23, world/world_generator.gd:41)
- Runtime streaming compares player z-position against oldest/furthest chunk ranges to spawn next chunk and reclaim stale chunk. (world/world_generator.gd:48, world/world_generator.gd:54, world/world_generator.gd:61)

## Chunk chaining behavior
- `ChunkGenerator.generate_chunk(...) -> Transform3D` returns end transform for next chunk. (world/chunk_generator.gd:33)
- World generator stores this as `next_transform` and randomizes next turn angle in `[-0.25, 0.25]` radians. (world/world_generator.gd:86, world/world_generator.gd:88)
- Chunks are driven by one cubic Bezier road curve (start/control/end points) used by both road and terrain blending math. (world/chunk_generator.gd:43, world/chunk_generator.gd:111)

## Terrain and road generation
- Terrain mesh uses resolution 80 grid and two-noise blend (`noise` + `detail_noise`). (world/chunk_generator.gd:5, world/chunk_generator.gd:16, world/chunk_generator.gd:136)
- Road mesh is generated from sampled Bezier segments and constrained by a max 10-degree cross-slope clamp for drivability. (world/chunk_generator.gd:262, world/chunk_generator.gd:280)
- Both terrain and road generate concave collision from mesh faces. (world/chunk_generator.gd:236, world/chunk_generator.gd:319)

## POI footprint blending
- POI placement candidate is rejected when distance to road is below POI-configured minimum. (world/chunk_generator.gd:87, world/chunk_generator.gd:90)
- Accepted POI sets local footprint radius/blend values used during terrain build to flatten and smoothly blend local ground around placement area. (world/chunk_generator.gd:94, world/chunk_generator.gd:95, world/chunk_generator.gd:172, world/chunk_generator.gd:177)

## Source files used
- world/world_generator.gd
- world/chunk_generator.gd
- world/poi_config.gd
