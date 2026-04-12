# RV Energy and Driving: Drive Control and Durability

## Scope
This document covers drive state control, input authority, chassis damage, and wheel-slot behavior.

## Drive control behavior
- Driver seat hold interaction enters driving mode by setting seat occupancy and calling `set_driving_state(true)` on connected RV. (equipment/driver_seat.gd:29, equipment/driver_seat.gd:45)
- Exiting seat restores player physics/camera and calls `set_driving_state(false)`. (equipment/driver_seat.gd:61, equipment/driver_seat.gd:76)
- Chassis reads WASD/Space when player-driving is active and always reads arrow keys as fallback remote/test control path. (rv/chassis.gd:235, rv/chassis.gd:244)
- If neither driving state nor fallback input is active, chassis enters parked stabilization branch (double brake, zero engine force). (rv/chassis.gd:247, rv/chassis.gd:250)

## Engine-force gate behavior
- Chassis calls energy step before applying throttle/reverse force. (rv/chassis.gd:274)
- If energy step returns false under active drive intent, engine force is blocked and brake fallback is applied. (rv/chassis.gd:278, rv/chassis.gd:279)

## Durability behavior
- Chassis has durability fields `max_chassis_health`, `current_chassis_health`, and `chassis_destroyed`. (rv/chassis.gd:53, rv/chassis.gd:54)
- `take_damage(amount)` ignores non-positive input and no-ops when already destroyed. (rv/chassis.gd:75, rv/chassis.gd:78)
- At zero health, chassis forces driving off and cuts engine force. (rv/chassis.gd:87, rv/chassis.gd:90)

## Wheel-slot system
- Chassis defines four wheel slots with position + steering/traction metadata. (rv/chassis.gd:10)
- Optional pre-install at ready uses slot table to create wheel nodes. (rv/chassis.gd:64, rv/chassis.gd:70)
- Runtime API supports `install_wheel`, `remove_wheel`, and installed wheel count. (rv/chassis.gd:305, rv/chassis.gd:312, rv/chassis.gd:321)

## Source files used
- rv/chassis.gd
- equipment/driver_seat.gd
- rv/fuel_filler.gd
