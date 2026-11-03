# makecode-microbit-extensions skill

A [Claude Code](https://claude.com/claude-code) / Agent Skill for:

- writing MakeCode code for the BBC micro:bit
- building MakeCode extensions (`pxt.json`, `//%` block annotations, help docs, tests)
- getting extensions approved by the Micro:bit Foundation so they show up in Extensions search

## Install

```bash
git clone https://github.com/ikaros-ch/makecode-microbit-extensions-skill ~/.claude/skills/makecode-microbit-extensions
```

Claude loads it automatically when a task matches.

Three slash-command skills ship in `skills/`. Install each next to the main skill (they read its reference files via `../makecode-microbit-extensions/`):

```bash
for s in mb-mkcd-code mb-mkcd-extension-make mb-mkcd-extension-approval; do
  ln -s ~/.claude/skills/makecode-microbit-extensions/skills/$s ~/.claude/skills/$s
done
```

- `/mb-mkcd-code`: write micro:bit programs, checking pins, I2C, radio, Bluetooth and V1/V2 differences
- `/mb-mkcd-extension-make`: research (datasheets, prior art, approved extensions) and build a new extension
- `/mb-mkcd-extension-approval`: audit an extension against the approval rules and walk through submission

## Files

- [SKILL.md](SKILL.md): layout, workflow, quick reference, common mistakes
- [blocks.md](blocks.md): block annotation reference, naming conventions, help pages
- [approval.md](approval.md): approval checklist and submission steps
- [hardware.md](hardware.md): V1 vs V2, edge connector pins, MakeCode platform limits
- [i2c.md](i2c.md): I2C patterns (register vs command-packet devices, timing, errors, endianness)
- [radio.md](radio.md): radio packet layout, payload limits, broadcast messages, radio vs Bluetooth
- [ble.md](ble.md): Bluetooth services, UART, yotta config, C++ extension example (blehid)
- [extensions.md](extensions.md): index of every Micro:bit Foundation-approved extension, grep by chip or board

## License

MIT
