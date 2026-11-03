# Block annotations

Annotations are `//%` comments directly above an `export function` (or on the `namespace`). JSDoc above them becomes the tooltip and parameter help.

```typescript
const enum ThingMode {
    //% block="fast"
    Fast = 0,
    //% block="slow"
    Slow = 1
}

//% color="#2a9d8f" weight=90 icon="" block="Thing"
//% groups='["Text", "Color"]'
namespace thing {
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
    export function setMode(mode: ThingMode): void { }

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
| `help=github:repo/docs/page` | Help link to `docs/page.md` in the repo. |
| `color` `icon` on namespace | Category color (hex) and Font Awesome 4 glyph as a `\uf...` escape. |

## Naming conventions (approval checks these)

- English names. camelCase for namespaces, functions, params, fields; PascalCase for enums and classes.
- No `get` prefix on accessors (`temperature`, not `getTemperature`).
- Spell out words; avoid acronyms except well-known ones (LCD, RGB, I2C).
- Block text lowercase except acronyms.
- Keep everything inside a namespace. A global enum needs a unique prefix (`ThingMode`, not `Mode`).

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
