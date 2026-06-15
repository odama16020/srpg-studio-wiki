# Official Plugin Patterns and Categories

**Sources:** Directory structure + header comments + implementation of files under `plugin_official/` (flat + $コンポジション API/, $ハイレベル/, WorldMatrix/ subdirs; ~200 .js total). All examples and categories below are derived exclusively from these files. No unofficial plugins are referenced.

Official plugins are provided by サファイアソフト (https://srpgstudio.com/). They serve as the canonical, supported examples of how to extend the engine.

Every file follows a consistent header format with Japanese description, "使用方法" (usage), author, and update history.

## High-Level Categories (by Filename Prefix)

- **ai- / $ai-**: AI scoring, move point visibility, multiple goals, disabled scores/combinations. Override collectors, scorers, auto actions, or AutoActionBuilder.
- **battle- / $battle-**: Battle visuals (background/foreground motion, anime, death, effect frames, motion color, seal/easy action msg, slow, unskippable, auto item, unit death common, music). Heavy use of UIBattleLayout, RealBattle, FlowEntries, custom effects.
- **calc- / $calc-**: Damage/avoid/weight/shoot/good-weapon/multiplier/effective calculations. Classic AbilityCalculator / DamageCalculator patches.
- **config- / $config-**: Config screen items, auto mode, message/enemy speed, marking, fullscreen/software fullscreen, go home, large map unit window.
- **custom-** (largest group): Data-driven extensions via custom params + keywords on units/classes/items/skills/states/weapons/graphics/sounds/music/animation frames. Also custom difficulty, drop, item (full package), mapsortie, map turn SE, NPC, pattern (AI), place event, quest, random trophy, rare weapon, skill, state, surprise attack, unit, unit music, weapon anime off / graphics / music / sound.
- **event-**: Adjacent place events, auto free, bonus shop, unit events (normal + active turn).
- **eventtrigger-**: Extra conditions for events (all area reached, choice show condition, first skill, quest, unit command on, unit count).
- **extra- / $extra- / extra_***: Clipping off, character background, class root tracking, dictionary/gallery/story conditions, explanation pos.
- **face-**: Class change face, disable reverse.
- **music- / $music-**: Seamless map/OP/battle music continuation.
- **other- / $other-**: Auto sortie, durability change/recovery, grayed shop items, hide image talk/rest command, reverse background anime, shop negotiator, sort stock items, unmovable weapons, class-up item only, GS map command, illust disabled, switch item usable, total level CC, weapon level ex.
- **renderer-**: Conditional show, global, unit renderer overrides.
- **scriptexecute-**: Extensions for "Script Execute" event commands (appear bookmark unit, blank player, change message layout, class change screen, communication, custom parameter, demo map, empty custom param, equip unit weapon, event jump, head swap, inventory control, objective/objective ex, recollection event, revive unit, unit menu, video player).
- **simulation-**: Cost, default/level filters for map simulation.
- **skill-**: Custom skill implementations (critical, debuff after battle, force capture, group state, invalidate ex, no fusion attack, parameter bonus, penetrating, quick ex, scout, stock).
- **state-**: State effects (follow leader, HP recovery).
- **system- / $system-**: Disable stock, interruption, item cap, startup, anime view, char chip animation, level cap, OP/BG/Battle seamless, over limit, str or mag.
- **terrain-**: Auto state, boundary cursor, chip changer, class footstep, event checker, parameter bonus, random reinforcements, skill condition, sound, weapon (terrain affects usability).
- **transition-**: Alpha blend, rotation, smoothly screen transitions.
- **variable-**: Dynamic level, item holder, item target info, reinforcements, unit ID by item use.
- **window- / $window-**: Base force sortie, bonus disabled, critical avoid, gold, map third parts, map thumbnail, marshal, skill reorder, sort units, state help text, unit menu bottom, unit simple, blinking item.

## Subdirectory / Advanced Groups

- **$コンポジション API/** (composition-*.js): Composition effects (burst transition, candle, light, marking, negate background, screen background, super face, timed choice, unit element, vibration, helloworld). Use createComposition + set* + composite. Performance note: creation is costly.
- **$ハイレベル/** (highlevel-*.js): Custom scene, mod, minigame, canvas, resolution, outside, resource viewer, state decoration, localize unit name, versus. Higher-level scene/screen + graphics interop.
- **WorldMatrix/** (worldmatrix-*.js): World matrix transforms (helloworld, turnover transition, vibration, zoom). Matrix set + draw + reset. Hardware rendering considerations.

## Universal Patterns Observed Across Official Plugins

1. **IIFE + Alias** (see overriding-patterns.md): Almost every file is `(function(){ var alias1 = Foo.bar; Foo.bar = function(){ var v = alias1.call(this,...); ... return v; }; })();`
2. **Always call the alias** with correct `this` (repeated explicit warnings in comments).
3. **defineObject(Base* , { ... })** for new FlowEntries, AutoActions, EventCommands, ConfigItems, custom item packages, etc. + registration via append/push/insert after alias.
4. **Data driven via .custom and keywords**: Editor sets custom params or custom keywords on data; plugin reads with `typeof` guard and dispatches. This is preferred over hard-coded IDs.
5. **DynamicEvent + EventGenerator** for any game state mutation from plugin code (items, drops, procs, messages).
6. **StraightFlow push** for inserting into battle/item/turn/experience/death sequences.
7. **_configure* or configureCommands** + appendObject for pluggable lists (commands, config items, extra screens, collectors, scorers, evaluators, wait parts, original event commands, etc.).
8. **Header docs**: Every file documents exact "使用方法" (how to set custom param, what keyword to use, which object name for script execute, game.ini notes, Ctrl+click editor tricks, etc.).
9. **Guard + env storage**: Config and one-time state stored in `root.getExternalData().env` with typeof checks.
10. **Skip mode respect**: All move/draw/FlowEntry implementations check skip modes.
11. **Namespace**: Keywords and custom keys are namespaced (often "Official_" or similar).

## Do's and Don'ts (Distilled from Official Code & Comments)

**Do:**
- Call alias before/after your logic (chaining).
- Use IIFE.
- Drive behavior from editor data (custom + keywords + original data) so designers can use without editing JS.
- Use DynamicEvent for effects.
- Document usage thoroughly in the header comment.
- Use LayoutControl / create*Object helpers / existing selection windows.
- Test with skip, multiple plugins, player + enemy sides (for items/AI), different difficulties.

**Don't (or "be careful"):**
- Omit the alias call.
- Access .custom without typeof guard.
- Hardcode pixel sizes/positions (use helpers; some $ files do for personal only).
- Forget that music seamless + "音楽の再生" event commands need special options.
- Create heavy objects (compositions) every frame.
- Assume single instance or ignore aggregation/filters for area effects.
- Full-replace methods unless you have reimplemented all side effects (some $ variants do this; riskier for compatibility).
- Pollute globals (IIFE + objects protect).

## How to Learn from Official Plugins

- Pick a category matching your goal (calc- for formulas, custom-item.js as reference for full custom items, scriptexecute-*.js for event command extensions, ai-*.js for AI trios).
- Read the header "使用方法" first.
- Trace the aliases to the exact objects/methods overridden (UnitCommand, AbilityCalculator, ItemPackageControl, PreAttack._push*, EnemyTurn._configure*, UIBattleLayout, etc.).
- Note how they combine custom params + alias + (sometimes) defineObject + DynamicEvent.
- The $ prefixed files sometimes show more aggressive or alternative variants.

These patterns, combined with the explicit extension points documented in Script/ comments (especially map-enemyturnai.js for AI and the many _configure*/configureCommands/pushFlowEntry sites), are the supported way to extend SRPG Studio.

## See Also

- [overriding-patterns.md](../overriding-patterns.md)
- [defineobject-flows-and-helpers.md](../defineobject-flows-and-helpers.md)
- [es5-compatibility.md](../es5-compatibility.md) (official code is the reference style)
- Topical pages (calculators, items, AI, commands, battle, events, UI) — each links back to specific official examples for that domain.
- The source files themselves under `plugin_official/` (the only approved reference implementations for "how the official team does it").

When the wiki is updated for new official plugins, add new categories or note new objects they alias/configure here, and update the relevant topical page.