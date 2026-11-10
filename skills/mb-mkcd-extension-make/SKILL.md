---
name: mb-mkcd-extension-make
description: Build a new MakeCode micro:bit extension (pxt-* repo, pxt.json, blocks, test.ts, docs) for a sensor, board or feature, researched from datasheets, existing drivers and approved extensions. Use when the user wants to create or design a MakeCode extension, or invokes /mb-mkcd-extension-make.
---

# Make a MakeCode micro:bit extension

Reference files live in the main skill folder, `../makecode-microbit-extensions/` (relative to this file). Read `SKILL.md` and `blocks.md` there before writing anything, plus `hardware.md`, and `i2c.md` / `radio.md` / `ble.md` for whatever the device uses.

## 1. Research first

- **Already exists?** Grep `extensions.md` for the chip, board and vendor. If an approved extension covers it, tell the user and stop unless they still want their own (e.g. missing features: then consider a PR to that repo).
- **More MakeCode drivers and micro:bit MicroPython ports**: grep `resources.md`.
- **Prior art in MakeCode**: `gh search repos "<chip> makecode" --limit 20` and `gh search code "<chip>" --filename pxt.json`. Read the closest ones: copy good patterns, note their bugs.
- **Datasheet**: web search for the official datasheet. Take the I2C address(es), register map, init sequence, timing (boot delay, conversion time), byte order and signedness from it. Cite the URL in a code comment.
- **Reference drivers**: the vendor's Arduino / MicroPython / CircuitPython library shows a working init sequence and calibration math. Credit it in the README and keep its license compatible with MIT.
- **Hardware**: which pins, 3.3 V or 5 V logic, current needs, V1/V2 conflicts (`hardware.md`).

Write down the findings in a short list before coding and show it to the user.

## 2. Build

Follow the layout and workflow in `SKILL.md`:
- `pxt.json` listing every file, `main.ts` in one namespace that won't clash with the vendor's or other authors' (search first), enums inside it, lazy init on first block call.
- Blocks per `blocks.md`: lowercase text, JSDoc tooltips (also on the namespace and each enum; no `/** */` file header), spelled-out names, `help=` links, groups, sensible `min/max/defl`.
- `test.ts` calling every block with `// EXPECT:` notes.
- `README.md` with product link, commented `blocks` examples, links to tutorials, the `for PXT/microbit` line.
- MIT `LICENSE.txt`, `icon.png` 300x200 under 100 KB (ask the user for a product photo; don't invent one).

Build the approval requirements in from the start (`approval.md`), so step 3 of the user's journey is just submitting.

## 3. Verify

- `mkc build` must pass (install with `npm i -g makecode` if missing; ask first).
- Say plainly what was not tested on hardware.
- Release: bump version, tag `vX.Y.Z`, GitHub release. Ask before creating or pushing a repo.

Then offer a MakeCode tutorial (SKILL.md "Tutorials") and `/mb-mkcd-extension-approval`.
