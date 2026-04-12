# Module: Player Equipment Interactions

## 1. Responsibility
- Define interaction contracts between player input, world interactables, inventory state, and equipment placement lifecycle.
- Define tablet crafting gate and station delegation contracts for RV-connected production.
- Expose climb-related interaction surface used by tests and runtime state transitions.

## 2. Boundaries and Dependencies
- In scope:
  - `player/player_interact.gd` interaction dispatch and hold timing.
  - `player/player.gd` inventory, placement state, and climb state hooks.
  - `equipment/equipment.gd` placement lifecycle (`start`, `confirm`, `cancel`).
  - `equipment/tablet_ui.gd` craft eligibility and dispatch.
  - `equipment/crafting_station.gd` spawned output contract.
- Out of scope:
  - RV internals beyond consumed methods/signals.
  - Vehicle movement and world generation.
  - Enemy AI behavior.
- Primary dependencies:
  - `props/interactable_item.gd` pickup caller contract.
  - RV duck-typed methods/signals consumed by equipment and tablet logic.

## 3. Entry Points and Public Surface
- `player/player_interact.gd`
  - `_physics_process(_delta)` resolves quick interact, hold interact, wheel install, and placement initiation.
- `player/player.gd`
  - `add_item`, `get_active_item_name`, `consume_active_item`
  - `enter_equipment_placement`
  - Climb surface: `_try_start_climb`, `_process_climbing`, `_abort_climb`, `_build_climb_motion`, `_compute_rv_position_delta`, `_sanitize_velocity_after_climb`
- `equipment/equipment.gd`
  - `start_placement`, `confirm_placement`, `cancel_placement`
- `equipment/tablet_ui.gd`
  - `on_open`, `_evaluate_craft_buttons`, `_craft_item`
- `equipment/crafting_station.gd`
  - `spawn_item(scene_path) -> bool`

## 4. Internal Structure
| Part | Role | Key Symbols | File |
|---|---|---|---|
| Interaction Dispatcher | Converts raycast + key timing into actions | `_physics_process` | `player/player_interact.gd` |
| Inventory and Placement Owner | Holds carried items, active slot, and placement state | `inventory`, `add_item`, `enter_equipment_placement` | `player/player.gd` |
| Placement Lifecycle Node | Owns ghost/confirm/cancel state transitions | `start_placement`, `confirm_placement`, `cancel_placement` | `equipment/equipment.gd` |
| Crafting UI Bridge | Applies RV/material/power/station gates | `_evaluate_craft_buttons`, `_craft_item` | `equipment/tablet_ui.gd` |
| Craft Spawn Executor | Instantiates crafted output and consumes power | `spawn_item` | `equipment/crafting_station.gd` |
| Pickup Item Adapter | Sends metadata into player inventory API | `interact` | `props/interactable_item.gd` |

## 5. Data Contracts
- Inventory slot contract:
  - each item dictionary has `name`, `is_large`, and `scene_path`.
  - carry capacity is six slots.
- Interactable contract:
  - quick path requires `interact(player)`.
  - hold path expects `interact_hold(player)` and mutable `hold_timer` on target.
- Placement contract:
  - `start_placement` moves object into ghost state and disables collision layers.
  - `confirm_placement` reparents and restores collision/material state.
  - `cancel_placement` restores original parent and transform.
- RV duck-typing contract used by this module:
  - methods/signals such as `has_materials`, `deduct_materials`, `has_usable_power`, `consume_power`, `inventory_changed`, `fuel_changed`, `power_changed`.
- Craft station contract:
  - nodes in `crafting_stations` group must expose `spawn_item(scene_path)`.

## 6. Configuration Touchpoints
- Interaction hold thresholds and placement trigger timing are in `player/player_interact.gd`.
- Placement mode behavior and climb tuning constants are in `player/player.gd`.
- Craft recipe map and UI text behavior are in `equipment/tablet_ui.gd`.
- Spawn power cost is in `equipment/crafting_station.gd`.

## 7. Failure Modes and Safeguards
- Large-item carry rule blocks dual-large-item pickup and slot-switch misuse.
- Hold-interaction release path falls back to quick interaction only when hold action was not completed.
- Placement confirmation is blocked unless ghost state marks placement as valid.
- Crafting flow blocks on missing RV, insufficient materials/power, missing station, or invalid scene resource.
- Legacy climb test expectations can drift when helper contracts are renamed or removed.

## 8. Related Design Docs
- `docs/design/player-interaction-and-equipment.md`

## 9. Source Files Used
- `player/player.gd`
- `player/player_interact.gd`
- `equipment/equipment.gd`
- `equipment/tablet_ui.gd`
- `equipment/crafting_station.gd`
- `props/interactable_item.gd`
- `tests/test_player_climbing.gd`
- `tests/test_player_climbing_runtime.gd`

## 10. Completeness Notes
- This module doc keeps only stable ownership and interface contracts.
- Detailed per-flow behavior, edge-case walkthroughs, and validation steps are intentionally maintained in the design doc.