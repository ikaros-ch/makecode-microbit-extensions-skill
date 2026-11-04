---
name: mb-mkcd-extension-approval
description: Get a MakeCode micro:bit extension approved by the Micro:bit Foundation so it appears in Extensions search. Audits the repo against every approval requirement, fixes gaps, and walks through submission. Use when the user wants their extension approved, listed or searchable, or invokes /mb-mkcd-extension-approval.
---

# Get a MakeCode extension approved

Reference files live in the main skill folder, `../makecode-microbit-extensions/` (relative to this file). `approval.md` there is the checklist; `blocks.md` covers naming and help pages.

## 1. Re-check the rules

Requirements change. Fetch https://makecode.microbit.org/extensions/approval and https://support.microbit.org/solution/articles/19000054952-package-approval, and compare with `approval.md`. Tell the user about any differences and update `approval.md` if they agree.

## 2. Check it isn't already listed

Grep `extensions.md`, then confirm against the live list: `gh api repos/microsoft/pxt-microbit/contents/targetconfig.json -H "Accept: application/vnd.github.raw"` and look for the repo under `packages.approvedRepoLib`.

## 3. Audit the repo

Go through every item in `approval.md` and check it from the files, not by asking: run the verification commands there (tag vs `pxt.json` version, release, icon size and bytes, license, repo description, `mkc build`). Also check:
- `pxt.json` `files` equals the source files in the repo, no more, no less.
- Every exported block has lowercase text, a JSDoc tooltip and a `help=` link whose `docs/*.md` exists.
- The namespace and every enum have their own JSDoc tooltip; no `/** */` file header. Enums sit inside the namespace with spelled-out names; the namespace is unlikely to clash (search approved extensions and GitHub).
- `test.ts` calls every block and has `// EXPECT:` notes.
- README: product link, commented `blocks` examples (no screenshots), tutorial links, `for PXT/microbit` line.
- C++ (if any) targets CODAL, not mbed.

Report a table: item, pass/fail, evidence, fix.

## 4. Fix

Fix what can be fixed in the repo. Ask the user before committing, tagging or pushing. Things only the user can supply: product page, product photo for the icon, accessories-list submission, contact details. Ask; never invent them.

## 5. Submit

Follow the Submit section of `approval.md`: the user fills the review form themselves with their own details. Give them the answers prepared from the audit to paste in. After submission, explain the PR on `microsoft/pxt-microbit` and that it goes live with the next editor release.
