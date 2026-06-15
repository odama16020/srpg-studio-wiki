# UI, Rendering, Windows, and Custom Graphics

**Sources:** `Script/base/base-objects.js` (BaseWindow, BaseWindowManager, BaseScrollbar, BaseNoticeView), `Script/singleton/singleton-rendercontrol.js` (all *Renderer: Text, Unit, Item, Skill, Window, Graphics, Number, Status, Chapter, Content, Title, WeaponType, Invocation, etc. + ColorValue, TextFormat, CacheControl), `Script/singleton/singleton-customcharchip.js` (CustomCharChip*, BaseCustomCharChip, _configure), `Script/window/*.js` and `windowmanager-*.js`, `Script/screen/*.js` (many _configure* for extra screens, config items, bottom windows, sentences), `Script/utility/utility-messageview.js` (BaseMessageView, FaceView, MessageViewControl), `Script/utility/utility-scrolltextview.js`, `Script/singleton/singleton-system.js` (LayoutControl), `api_references/manager.html` (GraphicsManager), `api_references/resource.html`, and overrides in `plugin_official/renderer-*.js`, `window-*.js`, `config-*.js`, `face-*.js`, `custom-weapongraphics.js`, `extra-*.js`, `highlevel-*.js`, composition/ and WorldMatrix/ subdirs.

## Windows & Scrollbars

Extend `BaseWindow`:

```js
var MyWindow = defineObject(BaseWindow, {
  initialize: function() { /* ... */ },
  getWindowWidth: function() { return 200; },
  getWindowHeight: function() { return 80; },
  drawWindowContent: function(x, y) {
    // your drawing, using TextRenderer, ItemRenderer, etc.
  }
});

var w = createWindowObject(MyWindow, parent);
```

**Critical:** For anything inheriting BaseScrollbar or complex windows, use the specialized factories:
- `createWindowObject`
- `createScrollbarObject` (wires getChildScrollbar etc.)

`BaseWindowManager` helps group multiple windows.

`BaseNoticeView` for small floating notices (used in some event commands).

## Renderers (The Drawing Primitives)

Located in singleton-rendercontrol.js. Call them from your draw* methods.

- `TextRenderer.drawText(x, y, text, length, color, font)`
- `TextRenderer.drawTextRange`, `drawCharText`, `getTextWidth` etc.
- `UnitRenderer.drawDefaultUnit(unit, x, y, colorIndex)`
- `ItemRenderer.drawItem(x, y, item, colorIndex, alpha)`
- `SkillRenderer`, `WeaponTypeRenderer`, `StatusRenderer`, `NumberRenderer.drawNumber`, `ChapterRenderer`, `ContentRenderer`, `TitleRenderer`, `InvocationRenderer`
- `WindowRenderer.drawWindow(x, y, width, height, textUI)`
- `GraphicsRenderer` for fills, etc.

Many take alpha, colorIndex (for palette swaps), reverse, scale.

## Custom Char Chip / Unit Rendering

`CustomCharChipGroup._configureCustomCharChip()`

```js
var alias = CustomCharChipGroup._configureCustomCharChip;
CustomCharChipGroup._configureCustomCharChip = function(groupArray) {
  alias.call(this, groupArray);
  groupArray.appendObject(CustomCharChip.MyRenderer);
};
```

Implement `BaseCustomCharChip`:

- `setupCustomCharChip(unit)`
- `moveCustomCharChip()`, `drawCustomCharChip(x, y, unit, colorIndex)`
- Per-unit `setCustomRenderer(unit, renderer)`

Units can override globally via `unit.setCustomRenderer` or data keyword/handle.

See `singleton-customcharchip.js` + official renderer-unit.js, custom-weapongraphics.js, highlevel-statedecoration.js.

## GraphicsManager (Low Level Drawing)

From `root.getGraphicsManager()` (available in draw cycles):

- `fill(color)`, `fillRange(x, y, w, h, color)`
- `drawText(x, y, text, len, color, alpha, font)`
- `createFormattedText`, clipping areas (push/pop)
- Cache: `createCacheGraphics`, `setRenderCache`, `copyCache`
- Advanced: `getCanvas()`, `createComposition()`, `createMatrix()`
- Map transform: `setMapAlpha/Reverse/Scale/Degree/Zoom`, `enableMapClipping`
- `disableFontFamilyCheck()`

## Message & Scroll Views

`BaseMessageView`, `FaceView`, `TeropView`, `StillView`, `MessageViewControl`, `ScrollTextView`.

Many face/illust/background plugins alias FaceView methods or message center pos (`$other-illustdisabled.js`, `face-*.js`, `extra-characterbackground.js`).

## Screens & Interop

Custom screens extend `BaseScreen`, implement `setScreenData`, move/draw cycles, `getScreenInteropData()` (returns interop object for title/background/music/textUI).

Registered via `screen-extra._configureExtraScreens` + ExtraControl checks, or direct SceneManager.addScreen.

Many official scriptexecute-*.js and highlevel-customscene.js demonstrate launching custom screens from events or title.

Config items, bottom windows, objective/quest sentences, marshal items etc. have their own `_configure*` append points.

## LayoutControl (Position Helpers)

`LayoutControl.getCenterX/Y`, `getMapAnimationPos(x, y, anime)`, `getPixelX/Y`, `getUnitCenterX/BaseY`, `getRelativeX`, `getObjectVisibleCount`, `getNotifyY`, window padding, etc.

Use these instead of hardcoding resolutions.

## Advanced Graphics (Composition, WorldMatrix, Canvas)

In plugin_official subdirs:

- Composition API (`$コンポジション API/`): `createComposition`, setImage, saturation/brightness/blur/hue/affin/blend/composite, reset. Costly to create — cache.
- WorldMatrix: transforms (scale/rotate/vibration/zoom) during draw without per-sprite changes. Requires hardware rendering in some cases. Reset matrices after.
- highlevel-*: custom scenes, canvas, resolution changes, mods, outside interop, minigames, vs mode, resource viewer, state decorations.

These still bottom out on the alias/defineObject + GraphicsManager patterns.

Warnings from sources: performance (createComposition), hardware draw mode requirements, explicit matrix reset, clipping, async draw considerations.

## Best Practices

- Always draw inside the proper `drawWindowContent` / `draw*Cycle` / `drawFlowEntry`.
- Use create*Object factories for windows/scrollbars.
- Respect alpha, skip modes (don't draw heavy effects when skipping), and parent clipping.
- For custom unit visuals prefer CustomCharChip or unit.setCustomRenderer over global renderer patches when possible.
- Namespace your appended config items / extra screens.
- For text, prefer the TextUI objects returned by LayoutControl or query* rather than raw colors/fonts for theme consistency.
- Test at different resolutions / software fullscreen / high-ppi.

## See Also

- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md) (BaseWindow contract)
- [root-sessions-api.md](root-sessions-api.md) (GraphicsManager, MediaManager details)
- `Script/singleton/singleton-rendercontrol.js` (the renderers — skim for available draw helpers)
- `Script/singleton/singleton-customcharchip.js`
- `Script/base/base-objects.js` (BaseWindow / BaseScrollbar)
- `plugin_official/renderer-*.js`, `window-*.js`, `config-*.js`, `face-*.js`, `extra-*.js`
- Composition / WorldMatrix / highlevel example files in plugin_official/ (for advanced graphics)
- `api_references/manager.html`, `resource.html`, `subasset.html` (InteropTextUI, Layouts, Dictionaries)

UI extensions range from simple alias color/pos tweaks to full custom scenes and post-process effects. Start with windows + renderers + custom char chip for most needs.