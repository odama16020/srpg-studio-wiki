# Unit Commands and Map Commands

**Key references:** The base map-unitcommand.js, map-mapcommand.js, base-listcommand.js, and related scene files. Registration and override patterns are shown in various official plugins.

Commands are the menu actions available to the player (and sometimes AI/events).

## Base Architecture

`BaseListCommandManager` (extended by UnitCommand, MapCommand, etc.):

- Manages a list of commands.
- `configureCommands(groupArray)` — the key extension point. Base populates the standard ones; you append custom ones after calling alias.
- `openCommand`, `moveCommand`, `drawCommand`, `rebuildCommand`, `isRepeatMoveAllowed` etc.

Individual commands extend `BaseListCommand` (or `UnitListCommand` for unit-specific):

- `getCommandName`
- `isCommandDisplayable(unit)` or similar
- `openCommand`, `moveCommand`, `drawCommand`
- Mode state machine inside (`_move*Mode` etc.)
- Often push StraightFlow entries for the execution phase.

Use `groupArray.appendObject(YourCommand)` (or insert) in the configure hook.

## UnitCommand (The Big One on Player Turn)

`UnitCommand = defineObject(BaseListCommandManager, { configureCommands: ..., ... })`

It registers a large set of built-in commands (visible when you select a unit):

- Attack, Item, Trade, Stock, Wait
- Talk, UnitEvent (command type), Village, Treasure, Occupation, Shop, Gate, etc.
- Fusion / Metamorphoze variants (BaseFusionCommand)
- Quick, Steal, Wand, Information, etc.

Many have dedicated *CommandMode objects that handle the cursor selection, preview, and action execution via flows.

Key hooks on UnitCommand / UnitListCommand:
- `configureCommands(groupArray)`
- `isRepeatMoveAllowed(unit)` (important for "move again" after certain actions)
- `endCommandAction`
- Per-command `isCommandDisplayable`

To add a completely new unit command:
1. `defineObject(UnitListCommand, { openCommand:..., moveCommand:..., drawCommand:..., getCommandName:..., isCommandDisplayable:... })`
2. Alias `UnitCommand.configureCommands` and `groupArray.appendObject(MyCommand)`

See also windowmanager-posmenu, weaponselect etc. for the selection UIs that commands often launch.

## MapCommand

Simpler — usually just Turn End + custom map-level actions (e.g. "check objective", custom menu commands).

```js
var alias1 = MapCommand.configureCommands;
MapCommand.configureCommands = function(groupArray) {
  alias1.call(this, groupArray);
  groupArray.appendObject(MyMapCommand);
};
```

MapCommand.TurnEnd is the concrete one that ends the player phase.

## Other Command Managers

- `RestCommand` (rest map): Quest, ImageTalk, Next, etc. + customs.
- `SetupCommand` (battle setup / sortie): UnitSortie, Sortie, etc.
- `TitleCommand`: NewGame / Continue / etc.

All follow the same `configureCommands` + append pattern (sometimes via `TitleScene._configureTitleItem` or equivalent).

## Command Layout & Mixer

`CommandMixer` + various launchers in `utility/utility-commandlayout.js` let you control screen layout, background, text UI for command screens.

Many official config / window plugins alias layout or bottom window configuration (`screen-unitmenu._configureBottomWindows`).

## Custom Command Implementation Notes

Your command object will typically:
- Decide visibility based on unit state, position, adjacent units, event flags, variables, custom params.
- Open a pos selector or item list or dedicated screen.
- On confirm, either do immediate effect or push a flow / call DynamicEvent.
- Return proper MoveResult (SELECT, CANCEL, END, CONTINUE).
- Respect repeat move rules if the command should allow moving again.

For commands that launch full screens, use `SceneManager.addScreen` or the executive screen controller patterns.

## Event Integration

- UnitEventType.COMMAND triggers when certain commands are used.
- Place events of type CUSTOM with PlaceCustomType.COMMAND.
- Many scriptexecute-*.js add or modify command availability (scriptexecute-unitmenu.js, scriptexecute-objectiveex.js etc.).

## Best Practices

- Append after the alias call so built-in commands come first (or insert at desired position).
- Implement `isCommandDisplayable` (and graying if needed) so players aren't confused by invisible or always-disabled entries.
- For "move again" commands (Quick, some custom), carefully manage `isRepeatMoveAllowed` and the unit's move state.
- Use existing selection windows (WeaponSelectMenu, ItemSelectMenu, PosMenu) via the windowmanager objects rather than reinventing cursor logic.
- Test with multiple units selected, repeat move, event-skipped turns, etc.

## See Also

- [overriding-patterns.md](overriding-patterns.md)
- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md)
- [events-dynamic-script.md](events-dynamic-script.md) (for command-triggered effects)
- The base map-unitcommand.js (study the existing AttackCommandMode / ItemCommandMode / TalkCommandMode etc. for patterns)
- base-listcommand.js in the base scripts
- Official plugin files that touch commands (search for configureCommands or appendObject patterns)
- The API reference for related event command and screen interop.

Commands are one of the most "visible" extension points for players and a great place to add quality-of-life or new tactical options.