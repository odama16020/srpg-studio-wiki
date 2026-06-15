# Getting Started with SRPG Studio Plugins

**Key references:** Structure and comments in the base script files (such as info.ini, executive-scriptcall.js, base-top.js, base-objects.js, map-enemyturnai.js, item-base.js), the engine's API reference documentation, and patterns + headers from the official example plugins.

This page gives the big picture before you dive into specifics. **Always read es5-compatibility.md first.**

## What Is a Plugin Here?

In SRPG Studio, "plugins" are .js files that are executed after the base overridable scripts have defined all their objects and functions. They extend or replace behavior by:

- Reassigning methods on singleton objects (alias pattern)
- Registering new objects into configuration arrays (configure + appendObject)
- Pushing custom entries into StraightFlow sequences
- Implementing the 6 BaseItem* objects for new item types
- Subclassing Base* via defineObject and returning them from hooks
- Overriding top-level ScriptCall_* in some cases

The base overridable scripts contain the **overridable foundation** (calculators, AI bases, item packages, command managers, flows, etc.). The official example plugins show the approved ways to hook in.

You do **not** edit Script/ directly (it would be overwritten on engine update or project refresh). You add .js files that patch at runtime.

## Typical Project Layout (Relevant to Plugins)

In a standard SRPG Studio project you will commonly encounter:

- A Script (or equivalent base scripts) folder — This contains the overridable foundation: calculators, AI base classes, item package definitions, command managers, flow logic, etc. These are the files you read to discover what to override or extend. Do not edit them directly, as they can be reset on engine or project updates.
- Official example plugins (often provided when the official samples are included with the project or engine) — These demonstrate supported patterns and are excellent references for "how the engine developers intend extensions to be written."
- The engine's API reference documentation (HTML files) — These document the objects and methods available on `root`, sessions, assets (Unit, Item, etc.), generators, and so on.
- User-Made / community plugins (often in a `Plugin/` folder or equivalent, or provided as zips on GitHub/Drive/itch) — These are unofficial extensions. They are excellent for inspiration and discovering real-world use cases. The wiki now includes a curated list of popular ones (with links to the Fandom User-Made Plugin Links page and notable repos like Goinza's) plus high-level summaries. See references/user-made-plugins.md. Do not copy code directly; always re-implement based on official patterns + the wiki. Some popular Japanese ones are only available as zips (e.g. certain namaemitei contributions) and are best discovered via the fandom hub or community Discord rather than direct source browsing.
- Data and resource folders (Graphics, Audio, Material, Save, etc.).

When developing plugins, you add or edit .js files in the location your project uses for plugins (commonly a Plugin or similar folder) so that they are picked up when building or running the game.

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

Copy the header style + IIFE + alias discipline exactly from an official plugin example such as calc-goodweapon.js (or any small official file).

## Key Things to Internalize Early

1. **ES5 only** — See es5-compatibility.md. var, classic for loops, indexOf, function, IIFE. No let, arrows, templates, includes, etc.
2. **Always alias + call the original** — See overriding-patterns.md. This is the #1 rule for compatibility with base + other plugins.
3. **Data-driven** — Use `.custom` on units/classes/items/skills/states + keywords for CUSTOM types (see custom-parameters-keywords-originaldata.md). Designers configure in the editor; your JS reacts.
4. **Use the factories** — createObject(YourProto), createWindowObject, createScrollbarObject. Never raw `new`.
5. **Flows for sequencing** — Many battle/item/turn sequences are StraightFlow + BaseFlowEntry. Insert via aliasing the _pushFlowEntries* methods (defineobject-flows-and-helpers.md).
6. **DynamicEvent for side effects** — The clean way to do HP changes, item grants, messages, state adds, etc. from code.
7. **Different paths for player vs AI** — Especially for items (BaseItemAvailability vs BaseItemAI) and commands.
8. **Skip modes** — Every move/draw implementation must respect complete skip / event skip / flow skip or fast-forward breaks.
9. **Sources of truth** — the base overridable script files for the actual definitions and hooks, the engine's API reference HTML documentation for public objects and methods, and the official example plugins for recommended implementation patterns.

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
11. The actual source files in Script/ and srpgs_official_plugin/ for the objects you plan to touch.

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

## Using This Wiki with Your LLM (Practical Path for Plugin Development)

Once you have the wiki files available to your LLM tool:

1. Point your LLM to the root `AGENTS.md` (or paste the "Recommended Starter Prompt" from it). This tells the LLM the important rules without you having to explain everything.
2. **Fandom check is mandatory first step** (your LLM must do this): Before any new code, go to https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links and look for existing plugins that do what you want. Download + use them (with credit) — it's usually faster, safer, and lower effort than writing new code.
3. Your LLM then reads wiki/index.md, then the relevant pages (always start with es5-compatibility.md + overriding-patterns.md for foundations).
4. Ask for complete plugins following the patterns in the wiki. Test as described in the Testing Tips below.
5. For ongoing work across chats: Keep the wiki folder in your LLM's context or re-upload key files as needed. Your LLM can propose additions to a *local copy* of log.md or a personal notes file if you want to remember decisions for this project. You do not need to update the central reference wiki.

See `AGENTS.md` (root) and the usage sections in `wiki/index.md` + `wiki/LLM-INSTRUCTIONS.md` for copy-paste prompts and more guidance. The goal is minimal extra learning — the wiki + Fandom + your LLM should do most of the heavy lifting.

## Maintenance & This Wiki (for those updating the reference)

This wiki is designed to be read by LLMs (and humans) to generate correct, compatible plugins quickly. See LLM-INSTRUCTIONS.md for how to keep it up to date when new Script versions or official plugins are added.

The index.md gives a categorized catalog.

Now go read es5-compatibility.md if you haven't, then pick a domain page and the corresponding source files.

Happy (ES5) plugin writing!