# Module Contract: Player Equipment Interactions - Inventory and Input

## Responsibility
This component owns player-carried item state and interaction timing contracts for pickup, hold interactions, and wheel installation.

## Entry points
- `Player.add_item(item_name, is_large, scene_path)` (player/player.gd:76)
- `Player.get_active_item_name()` (player/player.gd:171)
- `Player.consume_active_item()` (player/player.gd:176)
- `PlayerInteract._physics_process(_delta)` (player/player_interact.gd:8)
- `Prop.interact(player)` (props/interactable_item.gd:16)

## Data contracts
- Inventory slot payload contract: `{ name: String, is_large: bool, scene_path: String }`. (player/player.gd:76)
- Large-item carry contract: at most one large item can exist in inventory at once, and active-slot switching is blocked away from active large item slot. (player/player.gd:80, player/player.gd:103)
- Interactable hold contract: target must expose both `interact_hold(player)` and a mutable `hold_timer` field. (player/player_interact.gd:30)

## Timing contracts
- Wheel install hold threshold: 1.0s. (player/player_interact.gd:22)
- Generic hold threshold: 1.0s. (player/player_interact.gd:34)
- Quick-interact debounce after trigger: 0.5s physics pause. (player/player_interact.gd:39, player/player_interact.gd:51)

## Failure surface
- Missing required target methods/fields (`interact`, `interact_hold`, `hold_timer`) silently skips corresponding interaction path.
- Inventory full or large-item gating causes prop pickup rejection without destroying world item. (player/player.gd:78, props/interactable_item.gd:25)

## Source files used
- player/player.gd
- player/player_interact.gd
- props/interactable_item.gd
