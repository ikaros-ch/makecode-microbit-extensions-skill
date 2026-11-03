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
- [ ] **README has well-commented examples** as TypeScript / `blocks` fences, not screenshots. A teacher should understand its purpose and use.
- [ ] **README links to further educational materials and tutorials.**
- [ ] **Blocks are lowercase** unless they refer to acronyms.
- [ ] **Blocks have tooltips** (JSDoc) **and help links** (`//% help=github:...` → `docs/*.md`).
- [ ] **`test.ts` is comprehensive**: covers all functionality, documents how to test, what to expect, and what counts as pass/fail (e.g. `// EXPECT:` comments). Must not throw in the simulator if it uses non-simulated hardware.
- [ ] Submitter is on the **micro:bit developer Slack** and joined `#makecode` and `#makecode-pkg-owners` (support article wording, Nov 2026). Workspace: https://microbit-community.slack.com/ (sign in there if already invited). There's no public sign-up: tech.microbit.org/community no longer mentions Slack. Ask for an invite via https://support.microbit.org/ (submit a ticket, say it's for extension approval) or on https://forum.makecode.com/c/microbit/11. If the user can't get in, say so on the form; the Foundation can make exceptions.
- [ ] TypeScript naming: camelCase functions, namespaces, members; capitalized class names, enums and enum members (see blocks.md).
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

1. Fill the extension review form: https://form.jotformeu.com/90075019358357 (repo URL, product, contact details, the checklist above). The form needs the submitter's own details and Slack membership; ask the user for them, don't make them up.
2. Foundation reviews. Once accepted, the MakeCode team opens a PR on `microsoft/pxt-microbit` adding the repo to the approved list (`targetconfig.json`); a team member comments asking for approval, then it merges.
3. Approval goes live with the next editor release. Then search for the extension name in Extensions to confirm.

## After approval

- New releases are picked up automatically; no re-approval for updates.
- Don't rename the repo or change `blockId`s: approval is per repo and saved projects reference block ids.
- Exceptions: the Foundation may approve extensions missing a criterion, or decline any extension.
