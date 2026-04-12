# Design: RV Energy and Driving

## 1. Why This Matters
- RV mobility and onboard production both depend on energy decisions made every physics frame.
- This partition ties together driving, refueling, generator conversion, and durability damage.
- If these behaviors diverge, players can get soft-locked or bypass intended survival pressure.

## 2. Problem Statement
The game needs one coherent runtime model for:
1. when driving is allowed or blocked,
2. how fuel and power are consumed/charged,
3. how player refueling works,
4. how chassis durability shuts down the vehicle under heavy damage.

## 3. Goals and Non-Goals
- Goals:
  - Keep fuel and power as distinct resources with explicit clamping and signal updates.
  - Apply predictable frame-step energy behavior for driving, idling, and generator usage.
  - Keep refuel interaction simple and non-destructive on invalid inputs.
  - Keep monster-damage integration explicit through chassis durability.
- Non-goals:
  - Final numeric balancing targets for fuel and power economy.
  - Save/load persistence strategy for resource state.

## 4. Options Considered
- Option A: Split energy into separate controller node.
  - Pros: stronger separation and easier isolated testing.
  - Cons: higher integration overhead for existing chassis consumers.
- Option B: Keep energy in chassis authority with generator adapters.
  - Pros: single runtime authority for motion and energy outcomes.
  - Cons: larger chassis script and duck-typed generator coupling.
- Chosen approach: Option B, with module docs defining stable interface boundaries.

## 5. Constraints and Assumptions
- `step_energy_system(...)` is called every frame before final drive-force application.
- Generators are optional and discovered via `rv_power_generators` group membership.
- Refuel accepts only `Gasoline Can` and performs can exchange on success.
- Arrow-key fallback control path is currently intentional for non-seat control/testing scenarios.

## 6. Canonical Workflow
1. Driver enters seat via hold interaction; seat toggles chassis driving state on.
2. Chassis physics tick reads drive, brake, and steer intent.
3. Chassis runs generator pass, then parked drain or drive burn/charge path.
4. If drive fuel requirement fails, engine force path is blocked for that frame.
5. On exit-seat input, seat restores player control and toggles driving state off.
6. Refuel interaction routes through fuel filler to `refuel_from_player` and performs full-to-empty can swap on success.
7. Monster hits call `take_damage(amount)`; destroyed chassis forces drive-off behavior.

## 7. Interfaces and Contracts
- Chassis resource and control methods:
  - `set_driving_state`
  - `step_energy_system`
  - `consume_fuel`, `add_fuel`, `consume_power`, `add_power`, `has_usable_power`
  - `refuel_from_player`
  - `take_damage`
- Fuel filler routing contract:
  - `interact(player)` must resolve an ancestor chassis and call refuel.
- Generator contract:
  - `generate_power(rv, delta)` expects RV methods/fields used for fuel and power mutation.

## 8. Edge Cases and Failure Patterns
- Refuel no-op conditions:
  - invalid player contract,
  - wrong active item name,
  - near-full fuel state.
- Generator no-op conditions:
  - disconnected RV,
  - invalid rates,
  - missing required RV methods,
  - no fuel or no missing power.
- Energy underflow is prevented by clamped mutation helpers.
- Destroyed chassis blocks continued driving even if input is present.

## 9. Validation Checklist
- [ ] Verify fuel burn changes with throttle intensity and idle burn still applies.
- [ ] Verify parked power drain occurs when not driving.
- [ ] Verify generator conversion consumes fuel and adds power only when connected.
- [ ] Verify refuel succeeds only with `Gasoline Can` and returns an empty can item.
- [ ] Verify destroyed chassis state disables driving and propulsion output.
- [ ] Verify arrow-key fallback behavior remains intentional after control changes.

## 10. Related Modules
- `docs/modules/rv-chassis-energy.md`

## 11. Source Files Used
- `rv/chassis.gd`
- `rv/fuel_filler.gd`
- `equipment/generator.gd`
- `equipment/driver_seat.gd`
- `project.godot`
- `tests/test_player_climbing.gd`
- `tests/test_player_climbing_runtime.gd`
- `tests/test_monster_navigation.gd`

## 12. Completeness Notes
- This design doc keeps volatile runtime behavior and validation concerns outside module contracts.
- Open design unknowns remain: time-to-empty targets, time-to-full targets, and desired generator efficiency envelope.