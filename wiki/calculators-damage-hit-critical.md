# Calculators (Damage, Hit, Critical, Ability, Experience)

**Key references:** Primary definition and logic in the base script singleton-calculator.js (AbilityCalculator, DamageCalculator, HitCalculator, CriticalCalculator, etc.). Also referenced in other base files such as singleton-paramgroup.js and singleton-system.js, and illustrated in official plugins such as calc-*.js.

These singletons are the heart of combat math and the most commonly patched objects for balance mods, new skills, terrain effects, etc.

## AbilityCalculator

Basic derived stats from unit + weapon.

Key methods (all overridable):

```js
AbilityCalculator.getPower(unit, weapon)   // Str/Mag + weapon pow (physics vs magic branch)
AbilityCalculator.getHit(unit, weapon)     // weapon hit + (Ski * 3)
AbilityCalculator.getAvoid(unit)           // (Spd * 2) + optional terrain avoid (if class type allows)
AbilityCalculator.getCritical(unit, weapon)// Ski + weapon critical
AbilityCalculator.getCriticalAvoid(unit)   // Luk
AbilityCalculator.getAgility(unit, weapon) // Usually Spd, minus (weight - Bld/Str/Mag) if weight display enabled
```

**Common overrides:** Add custom "good weapon" bonuses, terrain/class bonuses, support/compatible already layered on top in higher calculators.

Example (from official):

```js
var alias1 = AbilityCalculator.getPower;
AbilityCalculator.getPower = function(unit, weapon) {
  var value = alias1.call(this, unit, weapon);
  return value + getGWStatus(unit, weapon, 0);
};
// similar for getHit, getCritical
```

(See the official plugin calc-goodweapon.js.)

## DamageCalculator

Core of `calculateDamage(active, passive, weapon, isCritical, activeTotalStatus, passiveTotalStatus, trueHitValue)`

Steps (override points marked):

1. `isHpMinimum` → return -1 (special)
2. `calculateAttackPower` (pow + compatible + support; * effective factor if `isEffective`)
3. `calculateDefense` (def/mdf + compatible + support; 0 if `isNoGuard`)
4. Subtract, then halve if `isHalveAttack` (and not broken by `isHalveAttackBreak`)
5. * critical factor if `isCritical`
6. `validValue` (clamp to min damage)

Important predicate methods (frequently overridden for skills/states/weapons):

- `isCritical(active, passive, weapon, isCritical, trueHitValue)`
- `isEffective(...)` — checks TrueHitValue + ItemControl.isEffectiveData + INVALID skill flag
- `isNoGuard(...)` — WeaponOption.NOGUARD or TrueHitValue
- `isHpMinimum(...)`
- `isFinish(...)`
- `isHalveAttack(...)` — passive weapon option or BATTLERESTRICTION skill
- `isHalveAttackBreak(...)`
- `isWeaponLimitless(active, passive, weapon)` — DataConfig or NOWEAPONDECREMENT skill

Factors:

- `getEffectiveFactor()` → `DataConfig.getEffectiveFactor() / 100`
- `getCriticalFactor()` → same for critical

`calculateAttackPower` and `calculateDefense` are good hook points for global multipliers.

## HitCalculator

`calculateHit(active, passive, weapon, activeTotalStatus, passiveTotalStatus)`

- If `root.isAbsoluteHit()` → 100 (or 99 vs immortal)
- `calculateSingleHit` = Ability hit + Compatible + Support
- `calculateAvoid` = Ability avoid + Compatible + Support
- `validValue` clamps to min/max percent; immortal forces <=99

Override `calculateSingleHit`, `calculateAvoid`, or `validValue` for custom hit/avoid formulas or caps.

## CriticalCalculator

Similar structure to Hit:

- `isCritical(active, passive, weapon)` first gate (checks INVALID.CRITICAL skill on passive + Miscellaneous.isCriticalAllowed)
- `calculateSingleCritical` + `calculateCriticalAvoid`
- `validValue` to 0-100

## Calculator (Attack Count, Rounds, Experience)

- `calculateAttackCount(active, passive, weapon)` → usually weapon.getAttackCount()
- `calculateRoundCount(active, passive, weapon)` → 2 if agility diff >= getDifference() and `isRoundAttackAllowed`
- `isRoundAttackAllowed`, `isCounterattackAllowed`
- `getAgilityPlus`, `getDifference`

Also contains recovery/damage item value calculators, sell price, total status creation, etc.

## SupportCalculator & CompatibleCalculator

These are consulted by the main calculators via `totalStatus` and direct calls.

**SupportCalculator** methods (getPower, getDefense, getHit, getAvoid, getCritical, getCriticalAvoid) aggregate from support data + skills + guests.

**CompatibleCalculator** does the same for weapon triangle / compatibility data.

Override at this level if you want global support/compat changes, or patch higher up.

## Experience & Growth

- `ExperienceCalculator.calculateExperience(...)`, `getBestExperience`, various `_get*Experience` factors.
- `ExperienceControl.obtainExperience`, `plusGrowth`, `directGrowth`, `obtainData`.
- Restricted variant for limited exp.
- `ParameterControl.changeParameter`, `adjustParameter`, `addDoping`.

Many growth/exp mods alias here or the obtain methods.

## ParameterControl / SymbolCalculator

For custom parameter display and symbol decorations on map.

## Common Extension Recipes

1. **Global multiplier** (e.g. difficulty, terrain):
   ```js
   var alias = DamageCalculator.calculateDamage;
   DamageCalculator.calculateDamage = function(...) {
     var dmg = alias.call(this, ...);
     if (dmg > 0) dmg = Math.floor(dmg * getMyFactor(active));
     return this.validValue(...); // or call original valid
   };
   ```

2. **New effective / halve / special weapon option logic:**
   Add checks inside the existing `is*` predicates (after or before calling alias).

3. **Custom agility or weight formula:**
   Override `AbilityCalculator.getAgility` or `Calculator.getAgilityPlus`.

4. **Custom exp curves per class/unit:**
   Alias `ExperienceCalculator._getExperienceFactor` or the obtain path + check unit.custom or class.

## DataConfig and DefineControl (Related Tunables)

- `DataConfig.isItemWeightDisplayable()`, `isBuildDisplayable()`, `isWeaponInfinity()`
- `DataConfig.getEffectiveFactor()`, `getCriticalFactor()`
- `DefineControl.getMinDamage()`, `getMinHitPercent()`, `getMaxHitPercent()`, `getMinCriticalPercent()` etc.

These are often the "knobs" that official $system-* plugins tweak.

## TrueHitValue and WeaponOption

Special attack modifiers passed down (from skills or events?):

- EFFECTIVE, NOGUARD, HPMINIMUM, FINISH, etc.
- WeaponOption: HPABSORB, NOGUARD, HPMINIMUM, HALVEATTACK*, SEAL*, etc.

See enumerated-types.md and constants.

## See Also

- [es5-compatibility.md](es5-compatibility.md)
- [overriding-patterns.md](overriding-patterns.md) (the alias recipe)
- [custom-parameters-keywords-originaldata.md](custom-parameters-keywords-originaldata.md) (how custom params feed into these)
- The base singleton-calculator.js (full source — read it; the actual math)
- Official plugins such as calc-goodweapon.js and related calc- variants, skill-parameterbonus.js, terrain-parameterbonus.js
- The API reference for DataConfig / DefineControl exposure (via root.getConfigInfo / UserExtension etc.)
- Attack flow pages for how calculators are called during real combat.

When overriding, remember that virtual attack simulation (for AI and preview) also goes through many of these — changes affect both player-visible numbers and enemy decisions.