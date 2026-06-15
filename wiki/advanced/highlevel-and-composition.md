# Advanced Topics: Custom Scenes, High-Level APIs, Composition, and WorldMatrix

**Key references:** Official plugins in the highlevel, composition, and WorldMatrix example groups, plus supporting usage in other official plugins and the API reference documentation (manager.html, volatile.html, asset.html, subasset.html). Base patterns are described in the main Script files.

These are higher-level or graphics-oriented extensions. They still rely on the core alias/defineObject/DynamicEvent patterns described elsewhere, but introduce additional objects and performance/ rendering requirements.

## High-Level Features ($ハイレベル/)

Examples cover:

- Custom scenes and mods (highlevel-customscene.js, highlevel-mod.js)
- Canvas and resolution control (highlevel-canvas.js, highlevel-resolution.js)
- Minigames, versus mode
- Outside interop, resource viewer
- State decorations, localize unit names

**Common elements:**
- Extend or alias scene/screen/command flows.
- Use `root.getGraphicsManager()` + canvas/composition for custom drawing surfaces.
- Interop data for custom screens (getScreenInteropData returning objects that provide title, background, music, text UI — see subasset.html InteropScreen).
- Often involve game.ini or hardware rendering considerations.
- Some demonstrate launching full custom experiences from title or events.

When implementing, follow the same registration (configure*, append/push) and lifecycle (enter/move/draw cycles) as normal screens/scenes.

## Composition API ($コンポジション API/)

Provides post-process / effect style operations on images/parts during draw.

Typical usage (from examples):

```js
var comp = root.getGraphicsManager().createComposition();
// or cache it
comp.setImage(someParts);  // or other source
comp.setSaturation(v);
comp.setBrightness(v);
comp.setGaussianBlur(v);  // or directional
comp.setHue(v);
comp.setColorChannel(...);
comp.setAffin(...);
comp.setBlend(...);
comp.composite(mode);  // CompositeMode
// later draw the result or use in other composition
comp.reset();
```

**Notes from sources:**
- `createComposition` is costly — create once, reuse, reset between uses.
- Combine multiple effects.
- Used for effects like blur, desaturate, color shift, transitions, special face/ background treatments, marking, vibration, light, candle, burst, unitelement, timed choice, negate background, screen background, super face.
- Works with the normal draw pipeline; some examples integrate with FaceView or battle layout.

Hardware vs software rendering may affect availability or quality of some composition features.

## WorldMatrix (Transforms)

Located in WorldMatrix/ subdir. Allows global or scoped transforms (scale, rotate, skew, vibration, zoom, turnover) that affect subsequent drawing without modifying every sprite/unit.

Typical:

- Create / set matrix on Graphics (or via manager).
- `setPrimitive`, `setScale`, `setRotation`, `setSkew`, multiply.
- Draw your content (units, map, effects).
- Explicitly restore / reset matrices afterward.
- Examples: helloworld, vibration, zoom, turnover transition.

**Caveats (from source comments):**
- Often requires hardware rendering mode.
- Matrix state must be carefully managed (reset after your draw to avoid affecting other UI).
- Useful for screen shakes, zoom on specific events, special transitions, versus or title effects.
- Combined with normal animation/motion.

## Integration with Core Patterns

Even in advanced files:
- Aliasing of draw/move methods on layouts, scenes, battles (UIBattleLayout, TitleScene, etc.).
- defineObject for custom flow entries or screen objects.
- DynamicEvent for sequencing advanced effects with messages/waits.
- Custom params / keywords to enable the effect on specific units/skills/items.
- Config items or extra screens to expose settings.
- Respect skip modes and performance.

## Performance & Compatibility Warnings

- Composition creation: do it sparingly (init time or cached).
- Matrix/clip state: always clean up.
- Test on target hardware (software fullscreen, different GPUs, high/low perf mode via root.isHighPerfMode()).
- Some features may be disabled or look different in release vs test play.
- Advanced drawing can interact badly with event skip, auto battle, or map scroll if not guarded.

## When to Use Advanced vs Core

- Most gameplay/balance/AI/item/command extensions: stick to calculators, BaseItem*, AI trio, commands, flows, custom params, DynamicEvent.
- Polish, unique presentation, custom UIs, minigames, special transitions: composition + WorldMatrix + custom scenes/screens.
- Start by studying the helloworld examples in the subdirs — they show the minimal skeleton.

## See Also

- [ui-rendering-windows.md](../ui-rendering-windows.md) (base renderers + GraphicsManager)
- [root-sessions-api.md](../root-sessions-api.md) (createComposition, matrix, canvas details)
- [defineobject-flows-and-helpers.md](../defineobject-flows-and-helpers.md) and [overriding-patterns.md](../overriding-patterns.md) (still apply)
- [references/official-plugin-patterns.md](../references/official-plugin-patterns.md) (category descriptions for the subdirs)
- The official example files in the composition, highlevel, and WorldMatrix groups (small, focused demos with headers explaining requirements)
- The API reference volatile.html (full Composition/Matrix/Canvas/Decoration specs)
- manager.html (GraphicsManager advanced methods)
- subasset.html (InteropScreen for custom scenes)

Advanced features let you create presentation that goes far beyond the default SRPG Studio look, but they require more care around state management, performance, and rendering mode. Always fall back to the core ES5 patterns and test thoroughly.