# Player Interaction and Equipment: Inventory and Input

## Scope
This document covers carry inventory rules and the raycast-driven interaction input loop.

## Inventory design
- Inventory is a fixed-capacity six-slot array of dictionaries with keys `name`, `is_large`, and `scene_path`. (player/player.gd:30, player/player.gd:31, player/player.gd:76)
- `add_item` rejects pickup when inventory is full or when a second large item is requested while one is already carried. (player/player.gd:76, player/player.gd:80)
- Picking up a large item forces the active slot to that item. (player/player.gd:90)
- Switching away from an active large item slot is blocked until that item is dropped/consumed. (player/player.gd:103)

## Input timing design
- Interaction runs from one per-frame raycast loop in `player_interact.gd::_physics_process`. (player/player_interact.gd:8)
- Wheel install is a special-case hold path: E held for 1.0s while looking at a target with `install_wheel` and holding `Wheel`. (player/player_interact.gd:18, player/player_interact.gd:22)
- Generic hold interaction requires both `interact_hold` and a mutable `hold_timer` field on the target. At 1.0s the target receives `interact_hold(player)`. (player/player_interact.gd:29, player/player_interact.gd:34)
- Releasing E before hold completion falls back to quick interact (`interact(player)`) for hold-capable targets. (player/player_interact.gd:44, player/player_interact.gd:49)
- Quick interact uses a 0.5s physics-process pause as debounce to prevent repeated trigger on the same collider. (player/player_interact.gd:39, player/player_interact.gd:51)

## Pickup and drop behavior
- Props call `player.add_item(item_name, is_large, scene_path)` and `queue_free` only on successful pickup. (props/interactable_item.gd:22, props/interactable_item.gd:25)
- Dropping active item spawns the source scene into the world in front of the player, then removes the inventory slot data. (player/player.gd:197, player/player.gd:203)

## Interface expectations
- Player API expected by props and chassis refuel flows:
  - `add_item(item_name, is_large, scene_path)`
  - `get_active_item_name()`
  - `consume_active_item()`
  (player/player.gd:76, player/player.gd:171, player/player.gd:176)
- Interactable target API expected by player raycast loop:
  - Optional `interact(player)`
  - Optional `interact_hold(player)` with mutable `hold_timer`
  - Optional `install_wheel()` for wheel path
  (player/player_interact.gd:18, player/player_interact.gd:30, player/player_interact.gd:49)

## Known validation gap
- `tests/test_player_climbing.gd` still expects `_can_begin_climb(...)`, which is not present in current player script.
  (tests/test_player_climbing.gd:32, player/player.gd:488)

## Source files used
- player/player.gd
- player/player_interact.gd
- props/interactable_item.gd
- tests/test_player_climbing.gd
