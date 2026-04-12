# Module Contract: RV Chassis Energy - Control and Damage Contracts

## Responsibility
This component defines driving-state transitions, refuel interaction proxy behavior, durability/destruction semantics, and wheel-slot interfaces.

## Driving-state contract
- `set_driving_state(state)` controls whether primary WASD driving input path is used. (rv/chassis.gd:142)
- Driver seat is the primary control bridge:
  - `interact_hold(player)` enters seat and toggles chassis driving on
  - `exit_seat()` restores player and toggles chassis driving off
  (equipment/driver_seat.gd:29, equipment/driver_seat.gd:76)
- Arrow keys are always accepted as fallback control path regardless of seat state. (rv/chassis.gd:244)

## Refuel contract
- `FuelFiller.interact(player)` resolves chassis ancestor and delegates to `refuel_from_player`. (rv/fuel_filler.gd:3, rv/fuel_filler.gd:8)
- `refuel_from_player` requires active item `Gasoline Can`, no-ops on full tank, and performs full-can to empty-can exchange through player API. (rv/chassis.gd:92, rv/chassis.gd:97, rv/chassis.gd:106)

## Durability contract
- Chassis joins `monster_damageable` group and accepts `take_damage(amount)` from attacker systems. (rv/chassis.gd:61, rv/chassis.gd:75)
- Destruction threshold (`current_chassis_health <= 0`) flips `chassis_destroyed`, disables driving, zeroes engine force, and applies braking. (rv/chassis.gd:87, rv/chassis.gd:90)

## Wheel-slot contract
- Slot schema stores wheel node name, local position, and steering/traction role. (rv/chassis.gd:10)
- `install_wheel() -> bool` installs first empty slot; `remove_wheel(slot_index)` removes by slot index. (rv/chassis.gd:305, rv/chassis.gd:312)
- Wheel hitboxes are generated and configured for interaction and monster collision support as part of wheel creation. (rv/chassis.gd:353)

## Source files used
- rv/chassis.gd
- rv/fuel_filler.gd
- equipment/driver_seat.gd
