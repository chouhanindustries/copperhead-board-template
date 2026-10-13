---
name: check-design
description: Say whether the design is sound right now by running copperhead check (ERC, DRC, doc drift, spec validation) and copperhead sync --dry-run, reported as one row per finding with where it is and what it means. Uses no model and changes nothing. Use for /check-design, "is the board clean", "run DRC", "what is wrong with the schematic", or before a release.
allowed-tools: Read, Grep, Glob, Edit, Bash(copperhead check:*), Bash(copperhead --json check:*), Bash(copperhead sync --dry-run:*), Bash(copperhead --json sync --dry-run:*), Bash(git status:*), Bash(git log:*), Bash(git rev-parse:*)
---

# Check the design

Two commands, neither of which uses a model or writes anything:

```bash
copperhead --json check            # ERC, DRC, doc drift, spec validation
copperhead --json sync --dry-run   # documents, constraints and pins.h against the design
```

`check` exits 1 and `sync --dry-run` exits 2 when they find something; that is
a finding, not a failure of the command.

## Report

Lead with one line: the commit checked (`git rev-parse --short HEAD`), whether
the tree is clean, and the count of findings by severity. Then one row per
finding:

| Severity | Check | Where | Finding | What it means |
| -------- | ----- | ----- | ------- | ------------- |

- **Where** is a reference, net, sheet or document: `U3 pin 4`, `net VBUS`,
  `docs/BOM.md row C12`. Never a coordinate alone.
- **What it means** is the consequence on a real board ("the regulator has no
  enable, so the rail never turns on"), not the rule restated.
- Group repeats: forty unconnected pins on one connector is one row with a
  count.

## Then

- **A real finding** gets a proposed `/change-design` request, one per fix.
  Never edit the design to silence a check.
- **A false positive** says why it is one. Known case: on a KiCad 10 project
  with several top-level sheets, ERC runs per sheet and reports global labels
  as isolated when their other end is on another sheet.
- **Unrouted connections** after a first build are expected and counted in
  `docs/LAYOUT.md`. Report the count; they block a release, not a review.

Write the result into the README's State table as `Last check`: the date, the
short commit and the counts, for example `2026-10-14 at 3f2a1c9: 0 errors,
2 warnings`. A check of an uncommitted tree is not recorded there.
