# Block annotations

Annotations are `//%` comments directly above an `export function` (or on the `namespace`). JSDoc above them becomes the tooltip and parameter help.

```typescript
// File header: use // comments. A /** */ here would become the tooltip of the next declaration.

/**
 * Blocks for the Thing sensor
 */
//% color="#2a9d8f" weight=90 icon="" block="Thing"
//% groups='["Text", "Color"]'
namespace thing {
    /**
     * How fast the thing runs
     */
    export enum Mode {
        //% block="fast"
        Fast = 0,
        //% block="slow"
        Slow = 1
    }

    /**
     * Show text at a position.
     * @param text what to show
     * @param col column 0-15
     */
    //% blockId=thing_show_text
    //% block="thing show $text|at column $col"
    //% text.defl="Hello" col.min=0 col.max=15
    //% group="Text" weight=100
    //% help=github:pxt-thing/docs/show-text
    export function showText(text: string, col: number): void { }

    //% block="thing mode $mode"
    export function setMode(mode: Mode): void { }

    //% block="thing color $color"
    //% color.shadow="colorNumberPicker"
    export function setColor(color: number): void { }

    //% block="on thing pressed"
    export function onPressed(handler: () => void): void {
        control.inBackground(() => { /* poll, then */ handler() })
    }
}
```

| Annotation | Effect |
|------------|--------|
| `block="..."` | Block text. `$param` or `%param` inserts a slot; `\|` breaks the line in external-inputs mode. |
| `blockId=ns_name` | Stable id. Set it once and never change it, or saved projects break. |
| `weight=N` | Order in the toolbox, higher first. |
| `group="X"` / `subcategory="Y"` | Grouping inside the category. |
| `advanced=true` | Moves the block under "more". |
| `p.min` `p.max` `p.defl` | Number slider range / default value. |
| `p.shadow="colorNumberPicker"` | Color picker (also `toggleOnOff`, `timePicker`, `turnRatioPicker`). Inline form: `$p=colorNumberPicker`. |
| `inlineInputMode=inline` | Keep all inputs on one row. |
| `expandableArgumentMode="toggle"` | Optional trailing params behind a +/- button. |
| `blockHidden=true` | Hidden helper block (shadow blocks, image literals). |
| `deprecated=true` | Hides the block but keeps old projects loading. |
| `help=github:name/docs/page` | Help link to `docs/page.md`. `name` is the pxt.json `name` (not the repo name), and the page must be listed in pxt.json `files`. |
| `color` `icon` on namespace | Category color (hex) and Font Awesome 4 glyph as a `\uf...` escape. |

## Naming conventions (approval checks these)

- English names. camelCase for namespaces, functions, params, fields; PascalCase for enums and classes.
- No `get` prefix on accessors (`temperature`, not `getTemperature`).
- Spell out words; avoid acronyms and abbreviations except well-known ones (LCD, RGB, I2C). `CustomCharacter`, not `Char`.
- Block text lowercase except acronyms.
- Keep everything inside the namespace, enums too (`thing.Mode`). A global enum needs a prefix no other author would use; generic prefixes like `Lcd` get rejected.
- JSDoc tooltip on the namespace and every enum, not just blocks. Check them in JavaScript mode.
- Pick a namespace nobody else is likely to use (the vendor may already own `groveLcd`). Nest under one unique name if needed: `namespace myVendor.thing { }`. Search approved extensions and GitHub first.

## Help pages (`docs/*.md`)

````markdown
# thing show text

Show text at a column.

```sig
thing.showText("Hello", 0)
```

## Parameters

* **text**: the string to show.

## Example

```blocks
thing.showText("Hello", 0)
```

```package
thing=github:user/pxt-thing
```
````

The `package` fence is what lets the help page render the extension's blocks.

Read generated help pages before releasing: a bad JSDoc once produced a page whose text was a dump of the source file.

## Block images (README on GitHub, slides)

Render real block PNGs with MakeCode's own renderer instead of screenshots. From a page served on `localhost` (MakeCode's own pages block `fetch` to localhost), embed an iframe of `https://makecode.microbit.org/--docs?render=1&lang=en`, wait for its `{type:"renderready"}` message, then:

```js
frame.contentWindow.postMessage({ type: "renderblocks", id, code,
    options: { package: "thing=github:user/pxt-thing", snippetMode } }, "*")
// reply: { type: "renderblocks", id, uri /* PNG data URL, 4x */, width, height, svg }
```

- `snippetMode: true` renders a single block or expression on its own; `false` wraps loose statements in `on start`.
- Several extensions: comma-separate them in `package` (`"a=github:u/a,b=github:u/b#v1.2.3"`).
- Write code that round-trips to blocks: `if/else`, not `? :`; drop optional arguments (`basic.showIcon(IconNames.Heart)`, not `..., 0`). Otherwise the image shows grey JavaScript blocks.
- Use the reply's `width` as the `<img width>`; the PNG is 4x for sharpness.
