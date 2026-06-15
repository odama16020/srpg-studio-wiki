# defineObject, Flows, and Core Helpers

**Key references:** The base base-top.js and base-objects.js (defineObject, createObject, Base* classes), utility-object.js and utility-event.js (StraightFlow, DynamicEvent), singleton-struct.js, and various flow-related files in attack/, item/, and map/. Official plugins provide concrete usage examples.

These are the fundamental building blocks for custom objects and sequenced behavior.

## defineObject and Instantiation

### defineObject (Prototype Inheritance)

```js
var defineObject = function(o) {
  // ... (internal: creates F with prototype o, merges additional arg objects)
  return n;
};
```

Usage for subclassing:

```js
var MyCustom = defineObject(BaseXXX, {
  enterFlowEntry: function(flowData) { ... },
  moveFlowEntry: function() { ... },
  // etc.
});
```

- First arg is the prototype (usually a Base* or another defined object).
- Additional args are mixed in (properties override).
- Does **not** call initialize.
- This is the ES5 replacement for `class MyCustom extends BaseXXX`.

### createObject (The Factory You Must Use)

```js
var obj = createObject(MyCustom);           // basic
var obj = createObjectEx(MyCustom, parent); // sets _parentObject, getParentInstance()
var win = createWindowObject(MyCustom, parent);
var bar = createScrollbarObject(MyCustom, parent);  // required for BaseScrollbar children
```

- Calls `initialize()` automatically if present on the prototype.
- Enforces `_masterMode` (from BaseObject) — if missing you get a `root.msg('object error')`.
- `create*` variants wire parent/child relationships expected by windows/scrollbars.

**Rule:** Never use `new` for engine-facing objects. Always `createObject(YourDefinedProto)`.

Related helpers: `createPos(x, y)`, `createRangeObject`, `validateNull`, array `appendObject`/`insertObject` extensions.

## BaseObject and Cycle Modes

All major objects inherit (via defineObject) from:

```js
var BaseObject = {
  _masterMode: 0,
  changeCycleMode: function(mode) { this._masterMode = mode; },
  getCycleMode: function() { return this._masterMode; }
};
```

Typical lifecycle in scenes, screens, flows, battles:

- `enterXXX(data)` or `setSceneData` / `setScreenData` → prepare + change mode.
- `moveXXXCycle()` / `moveXXX()`: switch on `var mode = this.getCycleMode();`, return `MoveResult.CONTINUE` / `END` / `SELECT` / `CANCEL`.
- `drawXXXCycle()` / `drawXXX()`.
- `backXXXCycle()` for cancellation paths.
- Many use `_prepareMemberData(data)` + `_completeMemberData(data)` inside enter (returns `EnterResult.OK` or `NOTENTER`).

Common mode/result enums (see enumerated-types.md): `EnterResult`, `MoveResult`.

## StraightFlow + BaseFlowEntry (Preferred for Extensible Sequences)

StraightFlow provides linear, insertable processing used heavily in attack, item use, experience, death, turns, etc.

Host usage pattern (inside a PreAttack, AttackFlow, ItemUseParent, MapSequence etc.):

```js
this._straightFlow = createObject(StraightFlow);
this._straightFlow.setStraightFlowData(this);
this._pushFlowEntries(this._straightFlow);  // or _pushFlowEntriesStart / _pushFlowEntriesEnd
this._straightFlow.enterStraightFlow();
```

Then in move/draw:

```js
if (this._straightFlow.moveStraightFlow() !== MoveResult.CONTINUE) { ... }
this._straightFlow.drawStraightFlow();
```

Extension point is almost always aliasing the `_pushFlowEntries*` method and calling `straightFlow.pushFlowEntry(YourEntry)` **after** the alias call.

### BaseFlowEntry Contract

```js
var BaseFlowEntry = defineObject(BaseObject, {
  enterFlowEntry: function(flowData) { return EnterResult.NOTENTER; },
  moveFlowEntry: function() { return MoveResult.END; },
  moveBackFlowEntry: function() { return MoveResult.CONTINUE; },
  drawFlowEntry: function() {},
  isFlowSkip: function() { return CurrentMap.isCompleteSkipMode(); }
});
```

Real implementations (core + plugins) almost always do:

```js
var MyEntry = defineObject(BaseFlowEntry, {
  _dynamicEvent: null,  // or _capsuleEvent, _balancer, etc.
  
  enterFlowEntry: function(flowData) {
    this._prepareMemberData(flowData);
    return this._completeMemberData(flowData);
  },
  
  _prepareMemberData: function(flowData) {
    // store unit, item, etc.
  },
  
  _completeMemberData: function(flowData) {
    if (!this._isConditionMet()) {
      return EnterResult.NOTENTER;
    }
    this._dynamicEvent = createObject(DynamicEvent);
    // setup generator etc.
    return EnterResult.OK;
  },
  
  moveFlowEntry: function() {
    // often delegate to dynamic event or balancer
    if (this._dynamicEvent.moveDynamicEvent() !== MoveResult.CONTINUE) {
      return MoveResult.END;
    }
    return MoveResult.CONTINUE;
  },
  
  drawFlowEntry: function() { ... }
});
```

Many core entries also implement `isFlowSkip` or check skip inside.

**Plugin usage:** Alias the host's push method (see overriding-patterns.md) and insert your FlowEntry. Official plugins such as custom-drop.js provide full minimal DropFlowEntry examples using DynamicEvent.

## DynamicEvent (for Running Event Commands from Code)

Extremely common for item effects, custom drops, battle messages, state procs, etc.

```js
this._dynamicEvent = createObject(DynamicEvent);
var generator = this._dynamicEvent.acquireEventGenerator();

// Call any generator method (see root-sessions-api.md and generator.html)
generator.unitItemChange(unit, item, IncreaseType.INCREASE, isSkip);
generator.hpRecovery(target, value, RecoveryType.SPECIFY, isSkip);
// ... dozens more: goldChange, parameterChange, stateAddition, experiencePlus, etc.

this._dynamicEvent.executeDynamicEvent();
```

In move cycle of the caller:

```js
return this._dynamicEvent.moveDynamicEvent();
```

(See: utility-event.js in the base scripts, with heavy usage in official plugins such as custom-drop.js, and similar implementations in battle-autoitem.js, scriptexecute-*.js, custom-item.js.)

Related: `DynamicAnime` for animation side effects, `CapsuleEvent` + `EventChecker` for running map/talk/etc. events with proper execution marking.

## StructureBuilder (Common Data Structs)

Plugins frequently build these structs when calling into attack/item/AI systems or when implementing custom commands.

Key ones:

- `StructureBuilder.buildAttackInfo()`
- `StructureBuilder.buildAttackParam()`
- `StructureBuilder.buildItemTargetInfo()`
- `StructureBuilder.buildCombination()` (for AI)
- `StructureBuilder.buildVirtualAttackUnit()`, `buildAttackEntry()`, `buildAttackExperience()`
- `StructureBuilder.buildRangeMetrics()`, `buildCostData()`
- Message params, levelup view params, motion params, etc.

Usage:

```js
var info = StructureBuilder.buildAttackInfo();
info.unitSrc = active;
info.unitDest = passive;
info.battleType = BattleType.REAL;
// pass to attack functions or PreAttack
```

(See the base singleton-struct.js for the full builder; used throughout attack, map, item, and related base files.)

ScreenBuilder has analogous `buildShopLayout`, `buildUnitMenu`, `buildConfig` etc. for launching screens.

## Other Frequently Used Helpers

- `LayoutControl.getCenterX/Y`, `getMapAnimationPos`, `getUnitCenterX`, `getRelativeX/Y`, pixel/scroll conversions.
- `PosChecker.getUnitFromPos(x, y)`, `getTerrainFromPos`, `getSideDirection`, `getNearbyPos`.
- `IndexArray.createIndexArray`, `createRangeIndexArray`, `getBestIndexArray`.
- `Miscellaneous.isPhysicsBattle(weapon)`, `isCriticalAllowed`, `isExperienceEnabled`, `changeClass`, `playFootstep`, `getRandomBackgroundHandle`.
- `Probability.getProbability(percent)`, `getInvocationPercent`.
- `DamageControl.setDeathState`, `reduceHp`, `playDamageSound`.
- `CurrentMap.getX/Y`, `getIndex`, `getSize`, `getWidth/Height`, turn skip modes, `isCompleteSkipMode`.
- `root.getCurrentSession().createMapSimulator()` → startSimulation* + getSimulation* results.
- Renderers: `TextRenderer.drawText`, `UnitRenderer.drawDefaultUnit`, `ItemRenderer.drawItem`, `GraphicsRenderer`, `WindowRenderer` etc. (singleton-rendercontrol.js).

## Cycle / Enter / Move Idioms Summary

Most custom objects follow one of:
- Full scene/screen: `setSceneData` / `setScreenData` → `_prepare` + `_complete` → initial `changeCycleMode`.
- FlowEntry: `enterFlowEntry` (prepare/complete) + move/draw + return proper Result.
- Command: `openCommand` / `moveCommand` / `drawCommand` + mode states.
- EventCommand: `enterEventCommandCycle` etc.
- AutoAction: `enterAutoAction` / `moveAutoAction` / `drawAutoAction`.

Always return the documented result constants. Respect skip modes.

## See Also

- [overriding-patterns.md](overriding-patterns.md) for how to insert your defined objects.
- [es5-compatibility.md](es5-compatibility.md)
- [battle-flows-and-attacks.md](battle-flows-and-attacks.md) (heavy StraightFlow users)
- [events-dynamic-script.md](events-dynamic-script.md)
- The base base-top.js, base-objects.js, utility-event.js, and singleton-struct.js (read these directly for the authoritative implementations and comments).
- Many official plugins for complete small FlowEntry + DynamicEvent examples.