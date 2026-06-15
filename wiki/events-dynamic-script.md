# Events, Dynamic Events, and Script Execution

**Key references:** The base executive-scriptcall.js, utility-event.js, eventcommand-scriptexecute.js, and related eventcommand files, plus singleton-system.js. The API reference documentation (event.html, generator.html, etc.) and official plugins (scriptexecute-*.js and similar) provide further examples.

Events are the primary way designers script story and the main hook for plugins to inject behavior.

## ScriptCall_* Global Hooks

Defined in the base executive-scriptcall.js. These are called by the engine at key moments. Plugins can override them (rarely needed, but powerful for init/load).

- `ScriptCall_Initialize(startupInfo)` — only place where root may not be available yet.
- `ScriptCall_Setup()` — calls SetupControl.setup()
- `ScriptCall_Backup()`, `ScriptCall_Retry(customObject)`, `ScriptCall_Reset()`
- `ScriptCall_Load(customObject)`
- `ScriptCall_Enter(sceneType, commandType)`, `ScriptCall_Move`, `ScriptCall_Draw`
- `ScriptCall_CheckInput(reason)`, debug hooks
- `ScriptCall_AppearEventUnit(unit)`, `ScriptCall_NewCustomRenderer(unit)`
- `ScriptCall_GetWeapon(unit)`, `ScriptCall_isItemReused`, `ScriptCall_CheckItem`, `ScriptCall_GetUnitAttackRange`, `ScriptCall_GetSimulationFilterFlag`, `ScriptCall_GetMoveCostArray`, `ScriptCall_GetUnitMoveCource`
- `ScriptCall_TurnEnd()`, `ScriptCall_DisableTurnSkip()`, `ScriptCall_CheckGameAcceleration()`, `ScriptCall_GetFriendDirectoryName()`

These are good for global custom renderers, save data migration, acceleration toggles, etc.

## DynamicEvent + EventGenerator (Run Event Commands from JS)

The #1 tool for plugin side-effects.

```js
var dynamicEvent = createObject(DynamicEvent);
var generator = dynamicEvent.acquireEventGenerator();

generator.messageShow(unit, text, MessagePos.CENTER, false);
generator.hpRecovery(target, 10, RecoveryType.SPECIFY, false);
generator.unitItemChange(unit, item, IncreaseType.INCREASE, isSkip);
generator.experiencePlus(unit, 50, false);
generator.stateAddition(target, stateInvocation, isSkip);
// ... 30+ more methods (see generator.html and root.getEventGenerator docs)
generator.playSound(handle, 1);

dynamicEvent.executeDynamicEvent();
```

In the move cycle of whatever is hosting your logic:

```js
return dynamicEvent.moveDynamicEvent();
```

This integrates perfectly with skip modes, event backlog, existing event UI, etc.

See official plugins such as custom-drop.js, battle-autoitem.js, many custom-item implementations, scriptexecute-reviveunit.js, etc.

Related: `DynamicAnime` for playing map animations from code.

## CapsuleEvent & EventChecker (Run Existing Map Events)

For triggering talk, auto, place, map common, etc. events with proper "already executed" marking:

- `CapsuleEvent`
- `EventChecker`, `RestEventChecker`, `RestAutoEventChecker`
- `UnitEventChecker.getUnitEvent(unit, UnitEventType.XXX)`

Used when you want a plugin action to fire a designer-authored event.

## BaseEventCommand (Custom Event Commands)

For commands invoked via "Script Execute" event command (object name match) or as full custom event commands.

```js
var MyEventCommand = defineObject(BaseEventCommand, {
  enterEventCommandCycle: function() { ... },
  moveEventCommandCycle: function() { ... },
  drawEventCommandCycle: function() { ... },
  getEventCommandName: function() { return 'MyCustomCommand'; }
});
```

Register via alias:

```js
var alias1 = ScriptExecuteEventCommand._configureOriginalEventCommand;
ScriptExecuteEventCommand._configureOriginalEventCommand = function(groupArray) {
  alias1.call(this, groupArray);
  groupArray.appendObject(MyEventCommand);
};
```

Then in the event, set the script execute "object name" to your `getEventCommandName()` value.

See the base eventcommand-scriptexecute.js and official scriptexecute-*.js (many are implemented this way: scriptexecute-reviveunit.js, scriptexecute-customparameter.js, scriptexecute-inventorycontrol.js, etc.).

## Event Generators from Root

`root.getEventGenerator()` returns the live one (used by DynamicEvent).

It has methods for every event command type: message*, backgroundChange, music/sound, animationPlay, unitRemove/Assign, gold/item/parameter/skill/hp/damage/experience/classChange, forceBattle, chapter, location, map chip/pos ops, script execute, state, fusion, metamorphoze, bonus, trophy, durability, etc.

See the API reference generator.html for the full list.

## Unit / Key / Place / Battle Events

- `UnitEventChecker.getUnitEvent(unit, type)` (DEAD, INJURY, ACTIVETURN, BATTLE, COMMAND)
- `KeyEventChecker` for key/door style interactions
- Place events, talk events, auto events, opening/ending, communication, recollection, map common
- `root.getCurrentSession().getActiveEventUnit()`

Many eventtrigger-*.js official plugins add extra conditions (all area reached, unit count, first skill, quest complete, etc.).

## Event Command Introspection (During Execution)

While an event command is running you can query:

- `root.getEventCommandObject()`
- `root.getEventCommandType()`
- Specific Command* objects (CommandMessageShow, CommandItemChange, etc.) have getters for their parameters.

See the API reference eventcommand.html.

## Best Practices

- Prefer DynamicEvent + generator for any "do X from plugin code" (item effect, custom AI action result, battle proc, menu action). It handles skip, drawing, history, etc.
- For custom commands that need UI, combine with screens or pos selectors + generator for the actual mutation.
- When registering custom event commands, namespace the name string.
- Respect `isEventCommandSkipAllowed`, `root.isEventSkipMode()`, flow skip etc.
- For data-driven events, combine with OriginalContent / custom params on the event.
- Use `EventCommandManager.returnEnterCode()` after executeDynamicEvent in some contexts.

## See Also

- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md) (DynamicEvent details)
- [overriding-patterns.md](overriding-patterns.md) (registering custom event commands)
- The base executive-scriptcall.js
- utility-event.js in the base scripts
- eventcommand-scriptexecute.js
- The API reference generator.html, event.html, eventcommand.html
- Official plugins such as scriptexecute-*.js (many minimal working custom commands)
- eventtrigger-*.js, event-*.js (condition and trigger extensions) among the official plugins

Events + DynamicEvent + custom script execute commands give plugins deep integration into the story and designer tooling.