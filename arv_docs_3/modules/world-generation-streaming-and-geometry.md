# Module Contract: World Generation - Streaming and Geometry Contracts

## Responsibility
This component defines world streaming and chunk geometry contracts for runtime world continuity.

## World streaming contracts
- `WorldGenerator._spawn_next_chunk()` instantiates chunk node, executes `generate_chunk`, appends z-range metadata, and updates next-transform handoff state. (world/world_generator.gd:66, world/world_generator.gd:72, world/world_generator.gd:79, world/world_generator.gd:86)
- `active_chunks` element contract: `{ node, start_z, end_z }`. (world/world_generator.gd:7)
- `_process` contract: spawn new chunk when forward buffer is low; free oldest chunk when behind-buffer threshold is exceeded. (world/world_generator.gd:54, world/world_generator.gd:61)

## Chunk geometry contracts
- `ChunkGenerator.generate_chunk(start_transform, next_turn_angle, shared_noise, shared_detail_noise, shared_poi_spawner) -> Transform3D`. (world/chunk_generator.gd:33)
- Generates terrain mesh, road mesh, and navigation region each invocation. (world/chunk_generator.gd:54, world/chunk_generator.gd:55, world/chunk_generator.gd:56)
- Terrain height contract: `_get_terrain_height(gx, gz, micro_multiplier)` combines base + detail noise with configurable multiplier. (world/chunk_generator.gd:22)
- Road tilt clamp contract limits cross-slope to 10 degrees when matching sampled terrain edges. (world/chunk_generator.gd:280)

## Failure surface
- Missing player reference in world generator disables runtime streaming updates. (world/world_generator.gd:45)
- Geometry mesh/collision generation cost scales with RESOLUTION and road segment counts.

## Source files used
- world/world_generator.gd
- world/chunk_generator.gd
