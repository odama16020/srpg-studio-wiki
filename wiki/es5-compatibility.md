# ES5 Compatibility (Critical)

**Key references:** Analysis of the base overridable scripts (including singleton-calculator.js, map-enemyturnai.js, item-base.js and related files) and official plugins (calc-*.js, custom-*.js, ai-*.js etc.), cross-checked against the API reference documentation. Confirmed via searches: the core scripts and official plugins use only var, classic for loops, function declarations, and IIFEs — no let, const, arrows, or many modern array methods.

SRPG Studio plugins execute in a **very old JavaScript environment** (ES5-era or earlier, similar to legacy browser/embedded JS engines of the time the engine was built). Modern ECMAScript features are **not supported** or unreliable. Using them will cause runtime errors or silent failures that are hard to debug in-game.

**Always read this page first.** Every other wiki page repeats key rules with examples.

## Forbidden / Unsafe Features (Do Not Use)

- `let` and `const` declarations → Use `var` only.
- Arrow functions `() => {}` → Use `function() {}`.
- Template literals `` `text ${expr}` `` → Use string concat `'text ' + expr`.
- `for...of` loops → Use classic `for (var i = 0; i < arr.length; i++)`.
- Destructuring `var {x, y} = pos;` or `var [a, b] = arr;` → Manual assignment.
- Default function parameters `function foo(x = 1)` → Inside function: `if (x === undefined) x = 1;`.
- Spread `...arr` or `func(...args)` → Manual loops or `.apply`.
- `class` syntax → Use `defineObject(BaseXXX, { ... })` (see defineobject-flows-and-helpers.md).
- `.includes(value)` on arrays/strings → Use `indexOf(value) !== -1`.
- `.find()`, `.findIndex()`, `.some()`, `.every()` (in many cases) → Classic loops.
- `.forEach()`, `.map()`, `.filter()`, `.reduce()` (avoid in hot paths or when compatibility is required; core uses 0 instances) → `for` loops + manual result arrays.
- `Promise`, `async`/`await`, `Map`, `Set`, `WeakMap` etc. → Not available; implement simple state machines or arrays/objects.
- `Object.assign`, many `Object.*` / `Array.*` statics beyond basics.
- Block-scoped anything, `const` enum tricks, etc.

**Evidence from sources:** 
- The base scripts and official plugins contain only `var`, classic `for` loops, `function` declarations/expressions, `indexOf` usage where needed, and IIFEs.
- Searches for `\blet\b`, `\bconst\b` (as declarations), `=>`, `\.includes\(`, `\.find\(`, arrow in function position, and template backticks returned zero relevant hits in core + official.

## Required Patterns (Copy These)

### 1. IIFE Wrapper (Universal in Official Plugins)

Every plugin file should be wrapped to avoid global pollution:

```js
/*--------------------------------------------------------------------------
  Your plugin description here.
  使用方法: ...
--------------------------------------------------------------------------*/

(function() {

  // All your code here: aliases, defineObject, helper functions, etc.

  var alias1 = SomeObject.method;
  SomeObject.method = function(arg) {
    var result = alias1.call(this, arg);
    // your ES5 code
    return result;
  };

  var MyCustom = defineObject(BaseSomething, {
    // methods
  });

}) ();
```

### 2. Var Only + Classic Loops

Good:

```js
var i, count, arr, value;
arr = [1, 2, 3];
count = arr.length;
for (i = 0; i < count; i++) {
  value = arr[i];
  // ...
}
```

Bad (will fail):

```js
let i;
const arr = [1,2,3];
for (let v of arr) { ... }
if (arr.includes(2)) { ... }
```

### 3. Alias + .call(this, ...) (Most Important Rule)

See overriding-patterns.md. Always:

```js
var alias1 = AbilityCalculator.getPower;
AbilityCalculator.getPower = function(unit, weapon) {
  var value = alias1.call(this, unit, weapon);  // MUST use .call(this, ...)
  if (typeof unit.custom.goodweapon !== 'undefined') {
    // ...
  }
  return value;
};
```

Forgetting the alias call or `this` breaks chaining with other plugins/official code.

### 4. Safe .custom Access

```js
if (typeof unit.custom.myValue === 'undefined') {
  return 0;
}
value = unit.custom.myValue;
```

Never assume existence. Same for `class.custom`, `weapon.custom`, `item.custom`, `skill.custom`, `env` values, etc. (Sources: plugin_official/calc-goodweapon.js, many custom-*.js, Script/singleton/*).

### 5. String Building (No Templates)

```js
var text = 'Hello ' + unit.getName() + ', value=' + value;
```

### 6. Array Search / Filter (ES5 Safe)

```js
var found = null;
var i, len;
len = list.length;
for (i = 0; i < len; i++) {
  if (list[i].getId() === targetId) {
    found = list[i];
    break;
  }
}

var filtered = [];
for (i = 0; i < len; i++) {
  if (condition(list[i])) {
    filtered.push(list[i]);
  }
}
```

If you really need a reusable helper, define a local function inside your IIFE:

```js
function arrayIncludes(arr, val) {
  return arr.indexOf(val) !== -1;
}
```

### 7. Object Creation (defineObject + createObject)

See defineobject-flows-and-helpers.md. Never use ES6 `class` or `new` directly for engine objects.

## Polyfills / Helpers You Can Add Locally

Inside your `(function(){ ... })();` you may safely add small helpers if a modern pattern would make code much clearer, but prefer staying idiomatic to core:

```js
function indexOfPoly(arr, val) {
  var i, len;
  if (arr.indexOf) {
    return arr.indexOf(val);  // if ever supported
  }
  len = arr.length;
  for (i = 0; i < len; i++) {
    if (arr[i] === val) return i;
  }
  return -1;
}
```

## Additional Engine-Specific Caveats

- Some newer `root.*` or session methods may exist in newer engine versions; always guard with `typeof root.someNewThing === 'function'`.
- Test in both test-play and release builds.
- Skip modes (`CurrentMap.isCompleteSkipMode()`, root.isEventSkipMode(), flow `isFlowSkip()`) must be respected in move/draw to avoid breaking fast-forward.
- Hardware vs software rendering affects advanced drawing (see advanced/highlevel-and-composition.md).

## Best Practice Summary

1. Start every new plugin file by copying the header + IIFE skeleton from a small official plugin example such as calc-goodweapon.js (or any similar minimal official file).
2. Write your logic using only `var`, functions, classic for, string +, indexOf.
3. Alias before replacing; always invoke alias with proper `this`.
4. Guard every `.custom` and optional runtime value with `typeof`.
5. When you want to use a "nice" modern feature while writing, immediately rewrite it to the ES5 equivalent before saving.
6. Link back to this page from your plugin header comment.

Violating these is the #1 cause of "works in my LLM-generated code but crashes in SRPG Studio" issues.

## See Also

- [overriding-patterns.md](overriding-patterns.md)
- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md)
- [getting-started.md](getting-started.md)
- Key files to consult: base/base-top.js (defineObject/createObject), official plugins such as custom-item.js and calc-goodweapon.js, and representative base singleton and map files for style reference.

When maintaining this wiki, re-verify the "no modern syntax" claim by searching the current Script/ + plugin_official/ before claiming support for anything new.