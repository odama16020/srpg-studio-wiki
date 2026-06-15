# Getting Started with SRPG Studio Plugins

**Sources:** Structure and comments in `Script/info.ini`, `Script/executive/executive-scriptcall.js`, `Script/base/base-top.js`, `Script/base/base-objects.js`, `Script/map/map-enemyturnai.js` (AI comment), `item/item-base.js` (custom item comments), `api_references/` (Root, sessions, generators), and the consistent patterns + headers in every file under `plugin_official/`.

This page gives the big picture before you dive into specifics. **Always read es5-compatibility.md first.**

## What Is a Plugin Here?

In SRPG Studio, "plugins" are .js files that are executed after the base `Script/` has defined all its objects and functions. They extend or replace behavior by:

- Reassigning methods on singleton objects (alias pattern)
- Registering new objects into configuration arrays (configure + appendObject)
- Pushing custom entries into StraightFlow sequences
- Implementing the 6 BaseItem* objects for new item types
- Subclassing Base* via defineObject and returning them from hooks
- Overriding top-level ScriptCall_* in some cases

The `Script/` directory contains the **overridable foundation** (calculators, AI bases, item packages, command managers, flows, etc.). `plugin_official/` shows the approved ways to hook in.

You do **not** edit Script/ directly (it would be overwritten on engine update or project refresh). You add .js files that patch at runtime.

## Project Layout (Relevant to Plugins)

- `Script/` — Base definitions (load order in info.ini). Read these to find what to override.
- `plugin_official/` — Official extensions (your primary reference for patterns and "this is supported").
- `api_references/*.html` — Raw docs for the engine objects exposed on `root`, sessions, assets, generators, etc.
- `Plugin/` (and unused) — User-created unofficial plugins (use for ideas only; never name them in your own plugins or this wiki).
- Other folders (Graphics, Audio, Material, Save, etc.) are data or editor resources.

When developing, you typically edit .js files that will be placed in the project's plugin folder (or equivalent) so they are included when the game is built/run.

## Minimal Plugin Skeleton (ES5)

```js
/*--------------------------------------------------------------------------
  Short description of what this does.
  
  使用方法:
  1. Set custom param on units: { myValue: 5 }
  2. ...
  
  作成者:
  Your name or team
  
  更新履歴:
  2026-06-15 Created
--------------------------------------------------------------------------*/

(function() {

var alias1 = AbilityCalculator.getPower;  // example target
AbilityCalculator.getPower = function(unit, weapon) {
  var value = alias1.call(this, unit, weapon);
  
  if (typeof unit.custom.myValue !== 'undefined') {
    value += unit.custom.myValue;
  }
  
  return value;
};

})();
```

Copy the header style + IIFE + alias discipline exactly from `plugin_official/calc-goodweapon.js` (or any small official file).

## Key Things to Internalize Early

1. **ES5 only** — See es5-compatibility.md. var, classic for loops, indexOf, function, IIFE. No let, arrows, templates, includes, etc.
2. **Always alias + call the original** — See overriding-patterns.md. This is the #1 rule for compatibility with base + other plugins.
3. **Data-driven** — Use `.custom` on units/classes/items/skills/states + keywords for CUSTOM types (see custom-parameters-keywords-originaldata.md). Designers configure in the editor; your JS reacts.
4. **Use the factories** — createObject(YourProto), createWindowObject, createScrollbarObject. Never raw `new`.
5. **Flows for sequencing** — Many battle/item/turn sequences are StraightFlow + BaseFlowEntry. Insert via aliasing the _pushFlowEntries* methods (defineobject-flows-and-helpers.md).
6. **DynamicEvent for side effects** — The clean way to do HP changes, item grants, messages, state adds, etc. from code.
7. **Different paths for player vs AI** — Especially for items (BaseItemAvailability vs BaseItemAI) and commands.
8. **Skip modes** — Every move/draw implementation must respect complete skip / event skip / flow skip or fast-forward breaks.
9. **Sources of truth** — `Script/*.js` for the actual overridable code, `api_references/*.html` for the public Root/asset/session/generator API, `plugin_official/` for how the engine authors recommend doing things.

## Recommended Reading Order

1. es5-compatibility.md (non-negotiable)
2. overriding-patterns.md
3. defineobject-flows-and-helpers.md
4. custom-parameters-keywords-originaldata.md
5. One domain page that matches your goal:
   - Formulas/balance → calculators-damage-hit-critical.md
   - New items → item-system-custom-items.md
   - Smarter or new enemy AI → ai-enemy-behavior.md
   - New player actions → commands-unit-map.md
   - Post-battle or combat visuals → battle-flows-and-attacks.md
   - Triggering effects or custom event commands → events-dynamic-script.md
6. root-sessions-api.md + assets-and-datalists.md (for data access)
7. utilities-and-structs.md (builders and helpers you will call constantly)
8. enumerated-types.md (the numbers you pass around)
9. ui-rendering-windows.md (if doing visuals)
10. references/official-plugin-patterns.md (map your goal to existing official categories)
11. The actual source files in Script/ and plugin_official/ for the objects you plan to touch.

## Common First Plugins

- Stat bonus from custom param or terrain (alias AbilityCalculator + get* or calculators)
- Custom item with unique effect (full 6 BaseItem* package via ItemPackageControl)
- New unit command or map command
- Post-battle drop or state proc (flow insertion + DynamicEvent)
- AI scorer or collector for a new skill type
- Config item or extra screen

All of these are directly supported by the patterns in official plugins.

## Testing Tips

- Use test play heavily (root.isTestPlay()).
- Exercise skip modes (turn skip, event skip, auto battle).
- Test with the item/skill used by both player units and enemies.
- Combine with other official plugins to verify alias chaining.
- Check save/load, chapter transitions, guest units, reinforcements.
- For graphics: test software fullscreen, different resolutions, high/low perf.

## Maintenance & This Wiki

This wiki is designed to be read by LLMs (and humans) to generate correct, compatible plugins quickly. See LLM-INSTRUCTIONS.md for how to keep it up to date when new Script versions or official plugins are added.

The index.md gives a categorized catalog.

Now go read es5-compatibility.md if you haven't, then pick a domain page and the corresponding source files.

Happy (ES5) plugin writing!