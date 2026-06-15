# Item System and Custom Items

**Key references:** Core logic in the base item-base.js (ItemPackageControl and the BaseItem* classes), singleton-itemcontrol.js, individual item-*.js files, and singleton-system.js. Extensive patterns and examples are shown in official plugins such as custom-item.js and related files.

Items are one of the richest extension surfaces because of the explicit "custom item" package system.

## ItemControl Family (Query & Manipulation)

`ItemControl` (most used):

- `isWeaponAvailable(unit, item)`
- `isItemUsable(unit, item)`
- `getEquippedWeapon(unit)` / `setEquippedWeapon(unit, weapon)`
- `isEffectiveData(passive, weaponOrItem)`
- `isWeaponTypeAllowed(referenceList, weapon)`
- `decreaseItem`, `lostItem`, `deleteItem`, `compareItem`, etc.

`UnitItemControl`: possession array management (getItem, pushItem, cutItem, arrangeItem, getPossessionItemCount, isUnitItemSpace, getMatchItem...).

`StockItemControl`: similar for convoy/stock.

`ItemChangeControl`: higher level changeStockItem / changeUnitItem (handles increase/decrease/release with immediate flags for trophies etc.).

Many terrain/weapon restriction plugins alias `ItemControl.isWeaponAvailable` or `isItemUsable`.

Example pattern (official terrain-weapon.js):

```js
var alias1 = ItemControl.isWeaponAvailable;
ItemControl.isWeaponAvailable = function(unit, item) {
  // check terrain
  if (/* bad terrain for this weapon type */) return false;
  return alias1.call(this, unit, item);
};
```

## ItemPackageControl & the 6 Custom Item Hooks

This is **the** supported way to add brand new item types (ItemType.CUSTOM + keyword).

```js
ItemPackageControl.getItemSelectionObject(item)          // internal dispatch
ItemPackageControl.getItemUseParent(item)
ItemPackageControl.getItemInfoObject(item)
ItemPackageControl.getItemPotencyObject(item)
ItemPackageControl.getItemAvailabilityObject(item)
ItemPackageControl.getItemAIObject(item)

ItemPackageControl.getCustomItemSelectionObject(item, keyword)
... (the other five)
```

Default implementations return the built-in Base* for known ItemType. For CUSTOM you override the getCustom* variants (usually by aliasing all six).

If keyword matches your string, return your defined object; otherwise fall through to alias.

See overriding-patterns.md for the exact alias + if (keyword === My_getCustomKeyword()) pattern. A full working example is provided in the official plugin custom-item.js.

## The Six Base* You Implement for Custom Items

### BaseItemSelection

Controls targeting / range / who can be chosen.

- `enterItemSelectionCycle`, `moveItemSelectionCycle`, `drawItemSelectionCycle`
- `setUnitSelection`, `setPosSelection`, `setFirstPos`, `isPosSelectable`
- `getUnitFilter`, `getResultItemTargetInfo`
- `_getUnitOnlyIndexArray` etc.

Often uses `PosSelector` or builds index arrays via IndexArray + simulator.

### BaseItemUse + ItemUseParent

The actual effect execution.

`BaseItemUse`:
- `enterMainUseCycle` / move / draw
- `mainAction` (the core "do the thing")
- `validateItem`, `decreaseItem`, `getItemAnimePos`
- Flow management via `_pushFlowEntries` and `ItemUseParent`

`ItemUseParent` orchestrates title scroll → main use → exp gain flows.

Many custom items use `DynamicEvent` + generator inside `mainAction` or a custom FlowEntry (see defineobject-flows-and-helpers.md and events-dynamic-script.md).

### BaseItemInfo

What appears in the item info window / help.

- `setInfoItem`, `moveItemInfoCycle`, `drawItemInfoCycle`
- `drawRange`, `getInfoPartsCount`, `getItemTypeName`, `getWindowTextUI`

### BaseItemPotency

Used for preview numbers (e.g. "recovers 10 HP", "damage 8", potency value in menus).

- `setPosMenuData`, `drawPosMenuData`
- `getKeywordValue`, `getKeywordName`

### BaseItemAvailability (Self / Player Side)

```js
isItemAvailableCondition(unit, item)
isPosEnabled(unit, item, x, y)
isItemAllowed(unit, item)
```

Used when player is choosing/using. Distinct from AI path.

### BaseItemAI (Enemy / Simulation Side)

```js
getItemScore(unit, combination)  // higher = better choice for AI
getUnitFilter(unit, item)
getActionTargetType(unit, item)
```

**Important distinction** (comment in item-base.js):
> BaseItemAvailabilityは、自軍がアイテムを使用する場合に参照され、敵軍がアイテムを使用する場合はAI処理も管理しているBaseItemAIが参照される。

You often need both for items that enemies should be able to use intelligently.

## Item Types and Specific item-*.js

Core types (ItemType enum): UNUSABLE, RECOVERY, ENTIRERECOVERY, DAMAGE, DOPING, CLASSCHANGE, SKILLGET, KEY, QUICK, TELEPORTATION, RESCUE, RESURRECTION, DURABILITY, STEAL, STATE, STATERECOVERY, SWITCH, FUSION, METAMORPHOZE, **CUSTOM:100**

Each has a dedicated implementation file in the base item scripts that provides the default Base* objects for that type. Custom items usually don't inherit those directly; they implement from the Base* in item-base.js and replicate needed logic (or delegate).

Range is handled via `ItemRangeControl.getRangeObject(item)` (returns {rangeValue, rangeType}).

## Common Custom Item Techniques from Official

- Read `item.custom` (or the item's original data) for parameters (power, range, state id, % chance, etc.).
- Use `DynamicEvent` + generator for all side effects (cleanest integration with events, skip modes, etc.).
- For area / multi-target: custom selection + custom use that loops or uses ALL range.
- AI scoring: implement `getItemScore` that simulates or estimates value (damage, recovery amount, utility).
- Exp on use: the ItemExpFlowEntry path is handled by the parent if you call the right methods or let the flow run.
- Durability / limited uses: `item.getLimit()` / decrease via the use object.

See the official plugin custom-item.js (the reference implementation) and similar advanced item plugins among the official samples for range/effect examples.

## Other Useful Item-Related

- `ItemIdentityChecker` (for stackability / reuse detection)
- `ItemRenderer.drawItem` / alpha variants for custom drawing
- Custom item color via renderer overrides
- Shop / stock sorting, grayed items, negotiator in official other-*.js and window-*.js
- Event commands: ItemChange, ItemUse, DurabilityChange etc. (can be triggered from your custom use)

## Best Practices

- Implement **all six** Base* even if some are minimal (prevents null crashes in info/AI windows).
- Support skip modes in your move/draw/use cycles.
- Make potency/score numbers accurate so AI and player previews match reality.
- Use keywords + custom params so one plugin file can support many designer-configured variants.
- Call `decreaseItem` / durability logic via the provided methods so limits and "unlimited" skills work.
- Test both player use and enemy AI use (different code paths).

## See Also

- [es5-compatibility.md](es5-compatibility.md)
- [overriding-patterns.md](overriding-patterns.md) (the 6 getCustom* aliases)
- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md) (DynamicEvent + FlowEntry inside items)
- [custom-parameters-keywords-originaldata.md](custom-parameters-keywords-originaldata.md)
- The base item-base.js (read the comments around BaseItemAvailability vs BaseItemAI and ItemUseParent)
- singleton-itemcontrol.js in the base scripts
- The official plugin custom-item.js (full reference custom item package)
- Individual item-*.js files in the base scripts for how built-in types implement the bases (copy patterns)
- The API reference item.html for the info structs (RecoveryInfo, DamageInfo, etc.)
- asset.html (Item asset methods) in the documentation.