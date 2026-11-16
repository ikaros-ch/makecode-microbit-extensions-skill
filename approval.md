# Getting an extension approved (official / searchable)

Approval is by the **Micro:bit Foundation**. Approved = shows in the Extensions search and its `icon.png` displays. Unapproved extensions still load by full URL.

Sources: https://makecode.microbit.org/extensions/approval and https://support.microbit.org/solution/articles/19000054952-package-approval. Re-check them; requirements change.

## Checklist (all must pass before submitting)

The review form makes the submitter tick each of these (form wording, Nov 2026):

- [ ] Supports a **commercially available** micro:bit accessory/sensor, and/or is for education, in line with the Foundation mission (https://microbit.org/about/#mission-statement).
- [ ] The accessory has been **submitted to the micro:bit accessories list**: https://form.jotformeu.com/83453273451355
- [ ] Uses **DAL/CODAL, not mbed** (mbed is V1-only). Pure TypeScript extensions pass; C++ shims must target CODAL.
- [ ] Primarily written in **English** (block translations welcome).
- [ ] **`icon.png`** in repo root, **300x200 px, under 100 KB**.
- [ ] **MIT licensed** (`LICENSE.txt` in root).
- [ ] **README links to the product page** or blog entry.
- [ ] **README has well-commented examples** as TypeScript / `blocks` fences, not screenshots. A teacher should understand its purpose and use. Use `blocks` and `sig` fences only: the reviewer rejected MakeCode-rendered block images in the README (Nov 2026), even with the fences kept under `<details>`. Start the README with a step-by-step quick start for people who just want to use it, then the reference.
- [ ] **README links to further educational materials and tutorials.**
- [ ] **Blocks are lowercase** unless they refer to acronyms.
- [ ] **Blocks have tooltips** (JSDoc) **and help links** (`//% help=github:...` → `docs/*.md`). Every **namespace and enum** also gets its own `/** */` tooltip. A `/** */` comment at the top of the file becomes the tooltip of whatever follows it (usually the first enum), so make file headers `//` comments. Check tooltips in JavaScript mode.
- [ ] **`test.ts` is comprehensive**: covers all functionality, documents how to test, what to expect, and what counts as pass/fail (e.g. `// EXPECT:` comments). Must not throw in the simulator if it uses non-simulated hardware.
- [ ] TypeScript naming: camelCase functions, namespaces, members; capitalized class names, enums and enum members (see blocks.md). The reviewer also checks:
  - **Enums inside the namespace** (`thing.CustomSlot.Slot1`), not global. A global enum needs a prefix nobody else would use; generic ones like `Lcd` are rejected because they block every other `Lcd...` name.
  - **Self-documenting names**: spell out words, no acronyms *or abbreviations* (`CustomCharacter`, not `Char`), except very common ones (LCD, RGB, I2C).
  - **A namespace unlikely to clash** (real case: `gamepad` was already used by the approved `bsiever/microbit-pxt-blehid`; search the sources of approved repos, not just their names) with the vendor's or another author's (`groveLcd` could be taken by Seeed). Clashes break user projects in ways users can't diagnose. To only need one unique name, nest: `namespace myVendor.thing { }` (as in [Seeed pxt-grove AHT20.ts](https://github.com/Seeed-Studio/pxt-grove/blob/master/sensors/AHT20.ts)). Not a blocker if a search finds no clash.

**Slack isn't required**: it isn't used anymore.

**Pre-review:** the Foundation welcomes an email to help@microbit.org (or a ticket at https://support.microbit.org/) with the repo link before submitting; the person who processes approvals gives informal feedback.
- [ ] **Finished**. Anything in development says "beta" in the repo description.

Also required by the MakeCode docs, not on the form:

- [ ] GitHub **release** tagged `vMAJOR.MINOR.PATCH`, matching `pxt.json` `version`.
- [ ] **`pxt.json` `files`** match the repo exactly.
- [ ] **GitHub repo description** set and searchable (product name, "micro:bit").
- [ ] README keeps the `for PXT/microbit` metadata line.

Verify the checklist mechanically before submitting:

```bash
git tag --list 'v*' | tail -1                 # matches pxt.json version?
gh release view --json tagName -q .tagName
python -c "from PIL import Image; i=Image.open('icon.png'); print(i.size)"   # (300, 200)
ls -l icon.png                                 # < 100 KB
gh repo view --json description,licenseInfo,visibility
mkc build
```

## Submit

1. Fill the extension review form: https://form.jotformeu.com/90075019358357 (repo URL, product, contact details, the checklist above). The form needs the submitter's own details; ask the user for them, don't make them up. **Submit one extension at a time** (the Foundation's advice, Nov 2026). Besides the checklist, the form asks:
   - **Associated tutorial?** Link a MakeCode tutorial if there is one (see "Tutorials" in SKILL.md). Optional, but worth adding before submitting.
   - **Pin allocation:** every pin the accessory uses and how (digital, analog, PWM, SPI, I2C, UART...). Count only what the extension itself claims, not pins used by examples or `test.ts`. A Bluetooth-only extension uses no pins; say so, and that `radio` can't be used alongside it.
   - **Accessories list:** for a hardware product, submit it to the accessories list first. A software-only or education extension answers "not applicable".
2. Foundation reviews. Once accepted, the MakeCode team opens a PR on `microsoft/pxt-microbit` adding the repo to the approved list (`targetconfig.json`); a team member comments asking for approval, then it merges.
3. Approval goes live with the next editor release. Then search for the extension name in Extensions to confirm.

## After approval

- New releases are picked up automatically; no re-approval for updates.
- Don't rename the repo or change `blockId`s: approval is per repo and saved projects reference block ids.
- Exceptions: the Foundation may approve extensions missing a criterion, or decline any extension.

## What the reviewer asked for (microbit-rc and grove lcd, Nov 2026)

- `pxt.json` has `"public": true`, and a short `description` (it is shown in a small space).
- No `yotta` Bluetooth config in an extension: users must be able to choose the pairing mode. Tell them which mode to pick instead. (In a custom config, use `"security_level": "SECURITY_MODE_ENCRYPTION_OPEN_LINK"`, not `null`.)
- blockIds as globally unique as `namespace_function`; after a namespace rename, rename the blockIds too.
- A `@param` line for every parameter, including enums and handlers.
- Icon without the micro:bit or MakeCode word marks (brand guidelines).
- README: no ambiguous "v1" (say "v1 of the extension"), a warning next to any APK download, docs for every part of a companion app.
- Events: the reviewer tests with slow handlers (a tone). Queued events then replay with the same final value; register with `EventFlags.DropIfBusy` and loop while a change counter moved.
- Tutorials: recheck UI steps against the live editor (Project Settings no longer has a Save button).
- Mark unfinished work with "- beta" in the repo About.
