# Battle Flows and Attacks

**Sources:** `Script/attack/attack_pre.js` (PreAttack, CoreAttack, many FlowEntries), `Script/attack/attack_flow.js` (AttackFlow, experience/death/weapon break flows), `Script/attack/attack_order.js` (NormalAttackOrderBuilder, VirtualAttackControl, AttackEvaluator.*), `Script/attack/attack_core.js`, `Script/attack/attack_realbattle.js`, `Script/attack/attack_commonbattle.js`, `Script/attack/attack_easybattle.js`, related in `Script/singleton/singleton-struct.js`, calculators (see separate page), `Script/utility/utility-event.js`, and overrides in `plugin_official/battle-*.js`, `custom-drop.js`, `skill-*.js`, `state-*.js` etc.

Battle is orchestrated via StraightFlow insertion points and evaluator registration — very plugin friendly.

## High Level Flow

Typical real battle:

1. PreAttack (setup, declarations, custom start flows)
2. Attack order / simulation (virtual attacks for hit/crit/damage/motion prediction)
3. AttackFlow (the actual sequence of attacks, damage application, state procs, exp, death, etc.)
4. RealBattle or EasyBattle UI (drawing, animations, cutins)
5. Post flows (drops, fusion catch/release, important item messages, loser messages, etc.)

Easy battle uses simplified path but still goes through much of the same calculators and flows.

## PreAttack

`PreAttack` manages the lead-in:

- `enterPreAttackCycle(attackParam)`
- `_pushFlowEntriesStart`, `_pushFlowEntriesEnd`
- Wraps `CoreAttack`
- Handles fusion attack data, force battle, lost events, etc.

Common insertions (official and custom):
- Unit declaration
- Custom start animations / messages
- Loser messages, weapon valid checks, drops, important items, fusion release/catch at the end

Alias example:

```js
var alias1 = PreAttack._pushFlowEntriesEnd;
PreAttack._pushFlowEntriesEnd = function(straightFlow) {
  alias1.call(this, straightFlow);
  straightFlow.pushFlowEntry(MyPreBattleEntry);
};
```

## AttackFlow

The main sequencer for the exchange of blows:

- Modes: STARTFLOW, ATTACK, ENDFLOW, COMPLETE
- `_pushFlowEntriesStart` (UnitDeclarationFlowEntry)
- `_pushFlowEntriesEnd` (UnitDeathFlowEntry, UnitSyncopeFlowEntry, Real/EasyExperienceFlowEntry, WeaponBrokenFlowEntry, StateAutoRemovalFlowEntry, DropFlowEntry, etc.)

`setAttackInfoAndOrder`, `startAttackFlow`, `checkNextAttack`, `finalizeAttack`, `executeAttackProcess` (HP reduce + state + item decrement).

Many FlowEntries in the end phase are perfect for custom post-battle effects (extra drops, custom exp, state changes, messages).

## Virtual Attack & Evaluators (for Order & Preview)

`NormalAttackOrderBuilder` (and Force variant) builds the sequence of who hits when, using virtual simulation.

- `_configureEvaluator(groupArray)` — register `AttackEvaluator.*` objects
  - Default: HitCritical, ActiveAction, PassiveAction, TotalDamage, AttackMotion, DamageMotion
- VirtualAttackControl, AttackEntry building

Override or extend evaluators to change predicted order, add custom motion rules, or factor new stats into "who goes first / how many hits".

This simulation feeds both the real execution order and the battle preview windows.

## Real / Easy Battle UI

- `RealBattle` / `EasyBattle` (BaseBattle subclasses)
- UIBattleLayout for drawing faces, gauges, backgrounds, foreground motion
- Custom effects via `BaseCustomEffect` + `pushCustomEffect`
- Motion, anime, sound, color overrides (many battle-*.js official plugins)

`battle-backgroundanimation.js`, `battle-changemotion.js`, `battle-foregroundmotion.js`, `battle-motioncolor.js`, `battle-deathanime.js` etc. are good references for visual extensions.

## Key Extension Points Summary

- PreAttack / AttackFlow `_pushFlowEntries*`
- AttackOrderBuilder `_configureEvaluator`
- BaseCustomEffect for new battle visuals
- Various *FlowEntry in attack/ for death, exp, weapon, state, drop, fusion
- UIBattleLayout methods (setBattlerAndParent, _drawBackground, moveBattleLayout)
- CoreAttack / RealBattle / Easy* for deeper motion control
- Calculators (is* predicates, damage formulas) — affect both preview and reality

## Drop, Death, Experience, Fusion Specifics

Drops are handled in dedicated FlowEntries at end of PreAttack/AttackFlow. Custom drop logic (extra items, conditional, % based on custom params) is a very common mod (see custom-drop.js pattern using DynamicEvent or direct trophy editor).

Death/syncope, experience (real vs easy), weapon durability break, auto state removal all have their FlowEntries that you can extend or replace.

Fusion catch/release also has dedicated end-of-battle handling.

## Skip and Performance

All battle flows must check skip modes (`CurrentMap.isCompleteSkipMode()`, flow `isFlowSkip()`) in move cycles so that "fast forward" and auto-battle work.

Drawing can be skipped in some paths.

## See Also

- [calculators-damage-hit-critical.md](calculators-damage-hit-critical.md) (the math called from here)
- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md) (StraightFlow + BaseFlowEntry contract)
- [overriding-patterns.md](overriding-patterns.md)
- `Script/attack/attack_pre.js` and `attack_flow.js` (the primary push points and default flow entries)
- `Script/attack/attack_order.js` (evaluators)
- `Script/attack/attack_realbattle.js` etc. for UI
- `plugin_official/battle-*.js`, `custom-drop.js`, `skill-debuffafterbattle.js`, `state-*.js`
- `api_references/volatile.html` (AttackInfo, VirtualAttackUnit, etc. structs)
- `api_references/asset.html` (Anime, OriginalMotion for custom battle graphics)

Battle extensions often combine calculator changes + flow insertions + custom effects. Virtual simulation means changes are "felt" by the AI preview and ordering as well.