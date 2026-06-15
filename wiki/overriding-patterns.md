# Overriding Patterns

**Key references:** Base files such as base-listcommand.js, map-unitcommand.js, map-mapcommand.js, map-enemyturn.js, item-base.js, singleton-itemcontrol.js, attack_pre.js, attack_flow.js, and utility-object.js, plus many official plugins (calc-goodweapon.js, custom-item.js, custom-drop.js, ai-*.js, battle-*.js, etc.), cross-referenced against the API documentation.

The primary way to extend SRPG Studio is **monkey-patching** (aliasing) existing singleton objects or registration via `_configure*` / `configure*` hooks + `appendObject` / `pushFlowEntry`. Direct inheritance via `defineObject` is used for new behaviors that are then registered.

## The Universal Aliasing Pattern (Most Common)

Official and community plugins almost always use this:

```js
(function() {

var alias1 = SomeCoreObject.someMethodName;
SomeCoreObject.someMethodName = function(arg1, arg2) {
  var value = alias1.call(this, arg1, arg2);
  
  // Your extension logic here (before, after, or conditional replace)
  if (typeof unit.custom.myBonus !== 'undefined') {
    value += unit.custom.myBonus;
  }
  
  return value;
};

})();
```

### Key Rules
- Use sequential `alias1`, `alias2`, ... (or descriptive names).
- **Always** call the original via `aliasX.call(this, ...)` (or `.apply(this, arguments)`). This preserves behavior for the base engine + any other plugins loaded before/after yours.
- Official comments repeatedly warn: forgetting the alias call means "他の開発者のスキルが考慮される機会がなくなる" (other developers' skills lose their chance to be considered).
- Place the alias **after** the base Script has defined the object (plugins load after base).
- Do the override inside an IIFE.

### Full Replacement (Rare, Risky)
Some official variants (`$` prefixed) do direct assignment without alias. Use only when you intend to completely replace behavior and have re-implemented everything needed. Risky for compatibility with other plugins.

Example skeleton from sources:

```js
var alias1 = AbilityCalculator.getPower;
AbilityCalculator.getPower = function(unit, weapon) {
  var value = alias1.call(this, unit, weapon);
  return value + getGWStatus(unit, weapon, 0);
};

var alias2 = AbilityCalculator.getHit;
AbilityCalculator.getHit = function(unit, weapon) {
  var value = alias2.call(this, unit, weapon);
  return value + getGWStatus(unit, weapon, 1);
};
// ... more
```

(See: the official plugin calc-goodweapon.js for a complete minimal example; also state-hprecovery.js, face-disablereverse.js, and similar variants.)

## Registration / Append Patterns

Many systems use an array of pluggable objects built at setup time. Override the builder method and append your implementation.

### Commands (UnitCommand, MapCommand, Rest, Setup, Title, etc.)

```js
var alias1 = UnitCommand.configureCommands;
UnitCommand.configureCommands = function(groupArray) {
  alias1.call(this, groupArray);
  
  // Append your custom command object (must be a BaseListCommand or UnitListCommand subclass)
  groupArray.appendObject(MyCustomCommand);
};
```

Similar for `MapCommand.configureCommands`, `RestCommand.configureCommands`, `SetupCommand.configureCommands`, `TitleScene._configureTitleItem`, `ConfigWindow._configureConfigItem`, `screen-extra._configureExtraScreens`, etc.

Sources in base scripts: map-unitcommand.js, map-mapcommand.js, base-listcommand.js, and the relevant scene files (scene-rest.js etc.), plus many official plugins (custom-*.js + window-*.js).

### ItemPackageControl (for ItemType.CUSTOM)

The canonical way to implement completely new item behaviors:

```js
var alias1 = ItemPackageControl.getCustomItemSelectionObject;
ItemPackageControl.getCustomItemSelectionObject = function(item, keyword) {
  if (keyword === My_getCustomKeyword()) {
    return MyItemSelection;
  }
  return alias1.call(this, item, keyword);
};

// Repeat for the other 5:
getCustomItemUseObject
getCustomItemInfoObject
getCustomItemPotencyObject
getCustomItemAvailabilityObject
getCustomItemAIObject
```

Then define:

```js
var MyItemSelection = defineObject(BaseItemSelection, { ... });
var MyItemUse = defineObject(BaseItemUse, { ... });
// etc. for all six Base* (see item-system-custom-items.md)
```

(See full: the official plugin custom-item.js and the base item-base.js.)

### Flow Insertion (Attack, PreAttack, Item Use, Turns, etc.)

```js
var alias1 = AttackFlow._pushFlowEntriesEnd;
AttackFlow._pushFlowEntriesEnd = function(straightFlow) {
  alias1.call(this, straightFlow);
  straightFlow.pushFlowEntry(MyCustomFlowEntry);
};
```

Common hosts:
- `PreAttack._pushFlowEntriesStart` / `_pushFlowEntriesEnd`
- `AttackFlow._pushFlowEntriesStart` / `_pushFlowEntriesEnd`
- `ItemUseParent` flows
- `AutoActionBuilder._pushMove` etc.
- Many scene/turn straight flows

(See: attack_pre.js and attack_flow.js in the base scripts, the official plugin custom-drop.js, battle-*.js, music-*.js.)

### AI Configuration

```js
// In map-enemyturn.js context (override the enemy turn object or alias the config methods)
var alias1 = EnemyTurn._configureCombinationCollector;  // or similar internal
// ...
```

Primary documented extension (from comment in source):

> AIを作成するとは、BaseCombinationCollector、BaseAIScorer、BaseAutoActionの3つを継承したオブジェクトを作成することを意味する。

Register new collectors/scorers via the `_configure*` methods on the turn object, then provide `CombinationCollector.XXX`, `AIScorer.XXX`, `XXXAutoAction`.

(See: the base map-enemyturnai.js lines 2-5, map-enemyturn.js, and official plugins ai-*.js, custom-pattern.js.)

### Other Common _configure Hooks

- `ParamGroup._configureUnitParameters` → add custom stats (see assets-and-datalists.md)
- `NormalAttackOrderBuilder._configureEvaluator`
- `WaitChain._configureWaitParts`, message analyzers
- `CustomCharChipGroup._configureCustomCharChip`
- `ScriptExecuteEventCommand._configureOriginalEventCommand`
- Simulation blocker/cost rules, map parts, etc.

Pattern is consistent: call original first, then `groupArray.appendObject(YourImpl)` (or insert at specific position for priority).

## defineObject for New Behaviors

See dedicated page [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md).

You rarely patch the base definition itself; you create a new object via `defineObject(BaseXXX, { ... })` and either:
- Return it from a `getCustom*` hook, or
- Push/append it into a flow or command list, or
- Register it in a `_configure*` array.

## Chaining and Load Order Considerations

- Plugins are loaded in some order (often alphabetical or filesystem; official sometimes use `$` prefix for variants).
- Your alias must call the previous implementation so later plugins still see a chain.
- For config arrays, append order affects execution/display order (earlier = higher priority in some AI/command cases).
- Use `insertObject` if you need precise positioning.

## Common Gotchas

- Overriding without calling alias → silent breakage of skills, states, supports, other plugins.
- Using modern JS inside the override → crash only on player machines with strict engine.
- Mutating shared structures without copying (e.g. index arrays returned from simulator).
- Forgetting that some methods are called on both player and enemy sides (availability vs AI paths for items).
- Not handling `isFlowSkip()` / complete skip modes in new FlowEntries → breaks fast-forward / auto-battle.
- Assuming `unit.custom.foo` exists.

## Recommended Skeleton for a Typical Patch Plugin

```js
/*--------------------------------------------------------------------------
  Brief description.
  使用方法:
  Set custom param on unit/class/item etc.
--------------------------------------------------------------------------*/

(function() {

var alias1 = AbilityCalculator.getPower;  // or whatever target
AbilityCalculator.getPower = function(unit, weapon) {
  var value = alias1.call(this, unit, weapon);
  // ES5-safe logic
  return value;
};

}) ();
```

For new objects + registration, combine alias on the registration point + one or more `defineObject(BaseXXX, ...)`.

## See Also

- [es5-compatibility.md](es5-compatibility.md)
- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md)
- [item-system-custom-items.md](item-system-custom-items.md)
- [ai-enemy-behavior.md](ai-enemy-behavior.md)
- Official plugins for dozens of real minimal examples (search for `var alias` patterns).
- Core registration sites in the base scripts: map-unitcommand.js (configureCommands and the many UnitCommand.*), item-base.js (ItemPackageControl), attack_*.js (flow push methods).