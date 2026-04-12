# Architecture

## 1. Purpose and Scope
- This document defines the current implementation-backed architecture baseline for ApocalypseRV.
- In scope:
  - world streaming and POI generation,
  - RV driving and energy systems,
  - player interaction, inventory, placement, climb interaction hooks, and crafting delegation.
- Out of scope:
  - multiplayer authority/network synchronization,
  - save/load persistence architecture,
  - full enemy AI architecture beyond world-integration touchpoints.

## 2. Goals and Non-Goals
### Goals
- Keep a continuous procedural road loop with bounded active chunk memory.
- Keep RV mobility and onboard equipment behavior coherent through fuel and power contracts.
- Keep player interaction contracts stable across pickup, placement, climb, and crafting flows.

### Non-Goals
- This baseline does not define final balancing values (economy or combat tuning).
- This baseline does not define a complete content roadmap from the full GDD.

## 3. System Context
- Engine: Godot 4.6.
- Physics: Jolt Physics.
- Renderer: GL Compatibility.
- Main scene entry: `res://world/test_world.tscn`.
- Core runtime loop: RV travels through procedurally generated highway chunks and scavenges POIs.

## 4. Component Map
| Component | Responsibility | Key Files | Depends On |
|---|---|---|---|
| World Streaming | Chunk add/remove lifecycle around player progression | `world/world_generator.gd` | Chunk Generation |
| Chunk Generation | Terrain, road, collision, and per-chunk nav strip generation | `world/chunk_generator.gd` | Noise, POI Spawner |
| POI Spawning | Weighted selection and building/loot/enemy spawn dispatch | `world/poi_spawner.gd`, `world/poi_config.gd` | Scene resources, Building Generator |
| Procedural Buildings | Procedural room expansion for configured POI types | `world/building/building_generator.gd` | Room scenes/config |
| RV Chassis and Energy | Driving-state energy arbitration, durability, and refuel entrypoint | `rv/chassis.gd`, `rv/fuel_filler.gd` | Driver Seat, Generators |
| Driver Seat | Enter/exit driving state and seat control bridge | `equipment/driver_seat.gd` | Chassis API, Player node |
| Generator | Optional fuel-to-power conversion for connected RV | `equipment/generator.gd` | Chassis resource API |
| Player Interaction | Raycast timing and quick/hold interaction dispatch | `player/player_interact.gd` | Player inventory and interactable contracts |
| Player Inventory and Placement | Carry state, placement state, and climb interaction hooks | `player/player.gd`, `equipment/equipment.gd` | RV hierarchy and world surfaces |
| Crafting UI and Station | Craft gating and output spawn delegation | `equipment/tablet_ui.gd`, `equipment/crafting_station.gd` | RV materials/power contracts |

## 5. Runtime Flows
### Primary flow
1. World scene starts and initializes world streaming with behind/current/ahead chunk buffers.
2. Chunk generation builds terrain, road, collision, and chunk-local nav region.
3. POI pipeline optionally spawns building, loot, and enemies from weighted config.
4. Player interacts with world items and equipment through raycast timing rules.
5. Placement mode runs ghost preview and confirm/cancel transitions.
6. Driver seat toggles RV driving state; chassis energy step arbitrates fuel/power per frame.
7. Tablet crafting checks RV/material/power/station constraints and delegates successful output spawn.

### Edge flow
1. Missing POI resources degrade to skip + warn behavior.
2. Nav region generation can skip in degenerate geometry cases and AI must fallback.
3. Refuel can no-op for invalid player/item preconditions or near-full tank.
4. Craft requests are blocked for missing RV, missing station, insufficient materials, or insufficient power.
5. Legacy climb test expectations can drift when helper contracts change.

## 6. Data and State Model
- World state:
  - active chunk window dictionaries with chunk node and z bounds.
  - next chunk transform and next turn angle handoff.
- POI state:
  - weighted table entries defining footprints, loot, enemies, and optional procedural config.
  - scene cache and warn-once missing-scene tracking.
- RV state:
  - fuel/power current and max values.
  - chassis durability and destroyed-state gate.
  - inventory dictionary used by crafting and refuel exchange.
- Player state:
  - 6-slot inventory dictionaries (`name`, `is_large`, `scene_path`).
  - placement mode state and climb mode state.

## 7. Interfaces and Contracts
- World contracts:
  - `generate_chunk(...) -> Transform3D` returns next chunk start transform.
  - `pick_poi() -> Dictionary` may return empty dictionary when no valid entries exist.
  - `spawn_building`, `spawn_loot`, `spawn_enemies` are optional spawn stages.
- RV contracts:
  - `step_energy_system(...) -> bool` returns whether frame drive-energy requirement is satisfied.
  - resource methods: `consume_fuel`, `add_fuel`, `consume_power`, `add_power`, `has_usable_power`.
  - `refuel_from_player(player)` and `take_damage(amount)` are integration entrypoints.
- Player/equipment contracts:
  - interaction targets expose `interact(player)` and optionally `interact_hold(player)` with `hold_timer`.
  - placement lifecycle uses `start_placement`, `confirm_placement`, `cancel_placement`.
  - craft stations expose `spawn_item(scene_path)` and are discovered via `crafting_stations` group.

## 8. Configuration and Environment
- Runtime entry scene configured in `project.godot`.
- World stream/chunk/noise/road tuning lives in world scripts.
- RV energy and durability tuning values are exported in chassis and generator scripts.
- Interaction thresholds and climb constants are configured in player scripts.
- Craft recipe map and station power cost are script-level config in tablet/station scripts.

## 9. Error Handling and Reliability
- World generation uses skip-and-warn behavior for missing POI resources.
- Resource mutation is clamped to prevent fuel/power underflow/overflow.
- Placement lifecycle restores state on cancel to avoid permanent ghost/collision corruption.
- Crafting flow is gated by layered precondition checks before resource mutation.
- Interface drift risk remains where systems depend on duck-typed methods/signals.

## 10. Security and Privacy Notes
- No external networking or account systems are in this architecture scope.
- No personal-data flows are present in the reviewed runtime scripts.
- The primary reliability risk is runtime contract drift, not data exposure.

## 11. Performance Notes
- Chunk terrain/road/collision generation is a known hotspot.
- Navigation strip generation and POI spawning add per-chunk overhead.
- Chassis generator polling is frame-based and scales with generator count.
- Runtime profiling evidence is still required for quantitative limits.

## 12. Observability and Debugging
- Primary observability remains script logging (`print`, `push_warning`, `push_error`).
- Current automated headless scripts in this scope:
  - `tests/test_monster_navigation.gd`
  - `tests/test_player_climbing.gd`
  - `tests/test_player_climbing_runtime.gd`
- Test contract drift must be checked whenever helper method surfaces change.

## 13. Testing Strategy and Coverage Map
| Area | Existing Tests | Missing Tests | Priority |
|---|---|---|---|
| World streaming and POI generation | No dedicated stream/POI integration script | Chunk continuity, POI distribution, nav-strip runtime checks | High |
| RV energy and refuel | No dedicated chassis energy script | Fuel/power mutation and refuel regression coverage | High |
| Monster navigation contracts | `tests/test_monster_navigation.gd` | Scene-level obstacle/path quality coverage | Medium |
| Player interaction and climb contracts | `tests/test_player_climbing.gd`, `tests/test_player_climbing_runtime.gd` | Interaction timing/placement regression scripts | High |
| Crafting flow | No dedicated crafting test script | UI gate and station-connection regression coverage | Medium |

## 14. Operations Notes
- Run game: `godot --path . res://world/test_world.tscn`
- Run headless script: `godot --headless -s <script.gd>`
- Run Python tools with project convention: `uv run <script>`

## 15. Risks and Open Questions
- Risk: missing POI resources can silently reduce content diversity despite graceful fallback.
- Risk: duck-typed interfaces across RV/equipment/tablet can regress without compile-time guarantees.
- Risk: no dedicated RV energy and world-streaming regression scripts in current suite.
- Open question: what are target fuel/power balance envelopes for intended play pacing?
- Open question: should crafting recipe data move from hardcoded map to external config?
- Open question: should legacy climb test expectations be updated or helper compatibility restored?

## 16. Glossary
- Chunk: streamed road-world segment with terrain, road, and optional POI content.
- POI: point-of-interest content package (building/loot/enemies).
- Nav Strip: per-chunk road-following navigation mesh region.
- Placement Ghost: temporary equipment preview state before confirm/cancel.
- Duck-Typed Contract: runtime method/signal assumption without strict interface type.

## 17. Source Files Used
- `docs/design/world-generation-and-poi.md`
- `docs/design/rv-energy-and-driving.md`
- `docs/design/player-interaction-and-equipment.md`
- `docs/modules/world-generation.md`
- `docs/modules/rv-chassis-energy.md`
- `docs/modules/player-equipment-interactions.md`
- `world/world_generator.gd`
- `world/chunk_generator.gd`
- `world/poi_spawner.gd`
- `world/poi_config.gd`
- `world/building/building_generator.gd`
- `rv/chassis.gd`
- `rv/fuel_filler.gd`
- `equipment/generator.gd`
- `equipment/driver_seat.gd`
- `player/player.gd`
- `player/player_interact.gd`
- `equipment/equipment.gd`
- `equipment/tablet_ui.gd`
- `equipment/crafting_station.gd`
- `props/interactable_item.gd`
- `tests/test_monster_navigation.gd`
- `tests/test_player_climbing.gd`
- `tests/test_player_climbing_runtime.gd`
- `project.godot`

## 18. Completeness Report
- Refreshed files:
  - `docs/design/world-generation-and-poi.md`
  - `docs/design/rv-energy-and-driving.md`
  - `docs/design/player-interaction-and-equipment.md`
  - `docs/modules/world-generation.md`
  - `docs/modules/rv-chassis-energy.md`
  - `docs/modules/player-equipment-interactions.md`
  - `docs/architecture.md`
- Coverage decisions:
  - Refreshed three design docs and three module docs as baseline core domains.
  - Did not create `docs/knowledge/*.md` because no external references were required in this refresh.
- Unknowns and assumptions:
  - Balance targets, persistence architecture, and dedicated regression depth are still open.
  - Legacy climb helper expectation mismatch remains an explicit known gap.
- Component split map:

| Component | Owning Code Paths | Target Detail Doc | Status |
|---|---|---|---|
| World Streaming | `world/world_generator.gd` | `docs/design/world-generation-and-poi.md` | Updated |
| Chunk Geometry and Nav Strip | `world/chunk_generator.gd` | `docs/design/world-generation-and-poi.md` | Updated |
| POI Dispatch and Config | `world/poi_spawner.gd`, `world/poi_config.gd` | `docs/design/world-generation-and-poi.md` | Updated |
| RV Energy Arbitration | `rv/chassis.gd`, `equipment/generator.gd` | `docs/design/rv-energy-and-driving.md` | Updated |
| Refuel and Drive Seat Bridge | `rv/fuel_filler.gd`, `equipment/driver_seat.gd` | `docs/design/rv-energy-and-driving.md` | Updated |
| Player Interaction Timing | `player/player_interact.gd` | `docs/design/player-interaction-and-equipment.md` | Updated |
| Inventory, Placement, and Climb Hooks | `player/player.gd`, `equipment/equipment.gd` | `docs/design/player-interaction-and-equipment.md` | Updated |
| Crafting UI and Output Spawn | `equipment/tablet_ui.gd`, `equipment/crafting_station.gd` | `docs/design/player-interaction-and-equipment.md` | Updated |

- Follow-up recommendations:
  - Add dedicated headless scripts for RV energy/refuel and world streaming.
  - Resolve legacy climb helper expectation drift in `tests/test_player_climbing.gd`.
  - Externalize recipe data if crafting scope expands beyond current hardcoded set.