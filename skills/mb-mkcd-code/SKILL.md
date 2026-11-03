---
name: mb-mkcd-code
description: Write MakeCode (Static TypeScript or blocks) programs for the BBC micro:bit V1/V2 that use pins, I2C, radio or Bluetooth correctly. Use when the user asks for micro:bit MakeCode code, or invokes /mb-mkcd-code.
---

# micro:bit MakeCode code

Reference files live in the main skill folder, `../makecode-microbit-extensions/` (relative to this file). Read the ones the task touches before writing code:

| Task touches | Read |
|---|---|
| any pins, V1/V2 differences, fibers, simulator limits | `hardware.md` (always) |
| I2C sensor/driver | `i2c.md` |
| radio | `radio.md` |
| Bluetooth | `ble.md` |
| a named board, chip or sensor | grep `extensions.md` first: use the approved extension instead of re-writing a driver |
| block-based output | `blocks.md` |
| no approved extension, or tools/testing (radio multi-editor, BLE test apps) | `resources.md` |

## Steps

1. Pin down the board: V1, V2, or both. If unknown, write code that works on both and say which parts are V2-only.
2. List every pin and bus the program uses. Check each against `hardware.md`: LED-matrix pins need `led.enable(false)`; P19/P20 are I2C only; radio and Bluetooth can't share a program.
3. If a device is involved, grep `extensions.md` for it. Found → use it (`github:owner/repo` in Extensions) and read its README/API with `gh api repos/owner/repo/contents/README.md -H "Accept: application/vnd.github.raw"`. Not found → follow `i2c.md` for the driver, and get facts (address, registers, init sequence) from the datasheet via web search, not memory.
4. Write the code in one file. Use events and `basic.forever` with pauses; no busy loops without a pause.
5. If `mkc` is installed, compile: put it in a temp project with `pxt.json` and run `mkc build`. Otherwise say it was not compiled.
6. Tell the user what can only be checked on hardware (I2C, BLE, radio range, RSSI).

Don't guess API names. If unsure, check https://makecode.microbit.org/reference or the target source (`microsoft/pxt-microbit`, `microsoft/pxt-common-packages`).
