# Design: Player Interaction and Equipment

## 1. Why This Matters
- This partition defines the player-facing interaction loop: pickup, carry, place, climb, and craft.
- It is the highest-frequency gameplay surface, so contract drift here quickly breaks progression.
- It also binds multiple duck-typed interfaces together (player, equipment, RV, station), making docs clarity critical.

## 2. Problem Statement
The runtime must coordinate:
1. interaction timing (quick vs hold),
2. inventory limits and large-item rules,
3. equipment ghost placement and finalize/cancel behavior,
4. climbing state transitions while RV is moving,
5. tablet crafting gate logic and spawn delegation.

## 3. Goals and Non-Goals
- Goals:
  - Keep interaction timing deterministic and readable from player input behavior.
  - Preserve strict inventory constraints for large-item handling.
  - Keep placement state reversible and physics-safe.
  - Keep climb movement resilient under moving RV transforms.
  - Keep crafting blocked until RV/material/power/station preconditions are met.
- Non-goals:
  - Full UI flow ownership outside tablet script behavior.
  - Final recipe progression or balancing catalog.

## 4. Options Considered
- Option A: Central interaction manager that owns all paths.
  - Pros: single place for timing and logging.
  - Cons: reduced locality with existing player/equipment ownership.
- Option B: Keep distributed ownership with explicit contracts between scripts.
  - Pros: matches current code organization and scene responsibilities.
  - Cons: higher risk of duck-typing drift.
- Chosen approach: Option B plus stronger module/design documentation split and contract notes.

## 5. Constraints and Assumptions
- Interaction relies on a single raycast dispatch loop in `player_interact.gd`.
- Equipment placement requires ghost state with collision disabled until confirm/cancel.
- Climbing logic currently does not expose legacy `_can_begin_climb` helper expected by one legacy test script.
- Tablet recipe set is currently hardcoded and minimal.

## 6. Canonical Workflow
1. Raycast loop resolves collider and applies E/F hold timing logic.
2. Pickup path sends item metadata into `player.add_item(...)`; on success, world item is removed.
3. Placement entry (F hold) starts ghost mode and switches player placement state.
4. During preview, player updates ghost transform/orientation and validity flag.
5. Confirm applies final parent/transform and restores collision/material state; cancel restores original state.
6. Climb start occurs from normal movement gates; climb loop applies RV delta compensation and contact grace checks; abort/exit applies reentry cooldown and velocity sanitization.
7. Tablet open resolves RV connection, updates craft button availability, and routes successful craft to a connected station spawn call.

## 7. Interfaces and Contracts
- Interaction target contracts:
  - `interact(player)` for quick interactions.
  - `interact_hold(player)` + `hold_timer` for hold interactions.
  - optional `install_wheel()` for wheel targets.
- Player inventory contract:
  - slot item payload is `{name, is_large, scene_path}`.
- Placement contract:
  - `start_placement`, `confirm_placement`, `cancel_placement` lifecycle.
- RV dependency contract in this partition:
  - methods/signals such as `has_materials`, `deduct_materials`, `has_usable_power`, `consume_power`, and inventory/fuel/power signals.
- Crafting station contract:
  - station must be in `crafting_stations` group and expose `spawn_item(scene_path)`.

## 8. Edge Cases and Failure Patterns
- Early key release after hold-start triggers quick-interact path when hold action did not complete.
- Large item carry lock blocks invalid slot changes and second-large pickup.
- Placement confirmation is rejected when preview validation fails.
- Crafting fails fast for missing RV, missing station, insufficient power, insufficient materials, or invalid scene.
- Legacy climb test drift:
  - `tests/test_player_climbing.gd` still expects `_can_begin_climb(...)`.
  - runtime climb contract script validates newer expectations including mantle-helper removal.

## 9. Validation Checklist
- [ ] Verify E-hold and quick-interact timing transitions behave as intended.
- [ ] Verify large-item constraints for pickup and slot switching.
- [ ] Verify placement confirm/cancel both restore stable physics/material state.
- [ ] Verify climb abort paths: manual detach, lost contact, ceiling hit, and RV spin guard.
- [ ] Verify tablet craft gating for RV/material/power/station preconditions.
- [ ] Verify craft station power cost consumption and spawn fallback behavior.
- [ ] Verify test expectations are updated when climb helper contract changes.

## 10. Related Modules
- `docs/modules/player-equipment-interactions.md`

## 11. Source Files Used
- `player/player.gd`
- `player/player_interact.gd`
- `equipment/equipment.gd`
- `equipment/tablet_ui.gd`
- `equipment/crafting_station.gd`
- `props/interactable_item.gd`
- `tests/test_player_climbing.gd`
- `tests/test_player_climbing_runtime.gd`

## 12. Completeness Notes
- This design doc stores volatile behavior and validation guidance that should not bloat module contract docs.
- Open unknowns remain around long-term recipe extensibility, climb difficulty targets, and explicit acceptance criteria for interaction feel tuning.