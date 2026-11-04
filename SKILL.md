---
name: makecode-microbit-extensions
description: Use when writing MakeCode code for the BBC micro:bit, building or publishing a MakeCode extension (pxt-* GitHub repo, pxt.json, //% block annotations, custom blocks), driving I2C/SPI hardware from MakeCode, or preparing an extension for Micro:bit Foundation approval so it shows up in Extensions search.
---

# MakeCode micro:bit extensions

## Overview

A MakeCode extension is a public GitHub repo (`pxt-<name>`) holding TypeScript (Static TypeScript) with `//%` comment annotations that turn functions into blocks. Users add it by pasting the repo URL into **Extensions**. It only appears in search once the **Micro:bit Foundation approves** it.

Static TypeScript is a subset: no `any` tricks, no closures over loop vars in old targets, no `Map`/`Set`, no regex, no `async`/`await`, no npm packages. Use `basic.pause`, `control.inBackground`, `pins.i2cWriteBuffer`, `Buffer`.

## Repo layout

```
pxt-thing/
  pxt.json      # manifest, lists EVERY source file
  main.ts       # the API + block annotations
  test.ts       # exercises every block; must compile
  README.md     # API docs + examples; keeps "for PXT/microbit" line
  LICENSE.txt   # MIT, required for approval
  icon.png      # 300x200, <100 KB, required for approval
  docs/*.md     # optional per-block help pages
```

Minimal `pxt.json`:

```json
{
    "name": "thing",
    "version": "1.0.0",
    "description": "Thing sensor for micro:bit",
    "license": "MIT",
    "dependencies": { "core": "*" },
    "files": ["README.md", "main.ts"],
    "testFiles": ["test.ts"],
    "supportedTargets": ["microbit"],
    "preferredEditor": "blocksprj"
}
```

`name`: lowercase letters, digits, `-`, `_`. Add `"radio": "*"` etc. only if used. Keep the package under 64 KB.

## Workflow

1. **Write** `main.ts` in one `namespace`, enums included. Block annotations and naming: see [blocks.md](blocks.md).
2. **Compile locally** before every push:
   ```bash
   npm i -g makecode      # once; provides `mkc`
   mkc build              # in the repo; compiles main.ts + test.ts for micro:bit
   ```
   (`pxt` CLI works too: `npm i -g pxt && pxt target microbit && pxt install && pxt build`.)
3. **Release**: bump `version` in pxt.json, commit, tag `vX.Y.Z` (exactly that format), push tag, create a GitHub release. `pxt bump` or the editor's GitHub "bump" checkbox do all of this.
4. **Test in the editor**: makecode.microbit.org → New project → Extensions → paste `https://github.com/user/pxt-thing`. Pin a version with `github:user/pxt-thing#v1.2.3`.
5. **Test on hardware**: flash `test.ts` with the device attached. The simulator does not emulate I2C peripherals; "runs without error" is all it proves.
6. **Approval**: see [approval.md](approval.md).

## Quick reference

| Need | How |
|------|-----|
| Toolbox category | `//% color="#2a9d8f" weight=90 icon="" block="Thing"` on the namespace |
| Block | `//% block="thing show $text at column $col"` above an exported function |
| Number range | `//% col.min=0 col.max=15 col.defl=0` |
| Group / subcategory | `//% groups='["Text","Color"]'` on namespace, `//% group="Text"` / `//% subcategory="Advanced"` on block |
| Event block | `export function onThing(handler: () => void)` + `control.inBackground` or `control.onEvent` |
| Help page | `//% help=github:pxt-thing/docs/show-text` → `docs/show-text.md` |
| Background loop | `control.inBackground(() => { while (on) { ...; basic.pause(20) } })` |
| I2C write | `pins.i2cWriteBuffer(addr, pins.createBufferFromArray([reg, val]))` |
| I2C read | `pins.i2cWriteNumber(addr, reg, NumberFormat.UInt8BE, true); pins.i2cReadNumber(addr, NumberFormat.UInt8BE)` |
| Pins, V1 vs V2, fibers, simulator limits | [hardware.md](hardware.md) |
| More I2C (command-packet boards, timing, errors, endianness) | [i2c.md](i2c.md) |
| Radio (packet layout, limits, broadcast, custom protocols) | [radio.md](radio.md) |
| Bluetooth (UART, yotta config, C++ shims, blehid) | [ble.md](ble.md) |
| Find an existing extension for a chip or board | grep [extensions.md](extensions.md), then [resources.md](resources.md) (more drivers) |
| Tools, radio multi-editor, reference drivers, schematics | [resources.md](resources.md) |

## Common mistakes

| Symptom | Cause / fix |
|---------|-------------|
| Extension not found in search | Not approved yet (expected), or README lost the `for PXT/microbit` line. Paste the full URL instead. |
| Editor keeps old code after release | MakeCode caches releases. Use `#vX.Y.Z` in the URL, or remove + re-add the extension, wait a few minutes. |
| File missing / compile error only in editor | File not listed in `pxt.json` `files`. Approval also requires the list to match the repo exactly. |
| Tag ignored | Tag must be `vMAJOR.MINOR.PATCH` and `pxt.json` version must match. |
| Peripheral half-initialised after power-on (e.g. LCD shows half the line) | Peripheral powers up with the micro:bit and misses early commands. Wait until `control.millis()` passes its reset time before init, and follow the datasheet init sequence exactly. Lazy-init on first block call. |
| Block param not shown | Block text param name must match the TS parameter name (`$text` ↔ `text`). |
| Image editor block | `//% imageLiteral=1 imageLiteralColumns=5 imageLiteralRows=8 //% shim=images::createImage` on a function returning `Image`. |
| Worry that `test.ts` ends up in user projects | It doesn't; `testFiles` only compile inside the repo. It still has to compile, and approval checks it covers every block with `// EXPECT:` pass/fail notes. |
| Approval rejected: README | It needs the product page link, commented `blocks` examples (no screenshots), and links to tutorials/educational material. |
