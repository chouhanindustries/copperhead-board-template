---
name: plan-bringup
description: Write the first power-on plan for a released revision in bringup/rev-<letter>.md (inspection, a current-limited power-up rail by rail, then each subsystem and interface) from the spec, pinout and BOM, with a place for every measurement, and record the results. Use for /plan-bringup, "the boards arrived", "how do I power this up safely", or to record bring-up measurements.
argument-hint: "[rev-letter]"
allowed-tools: Read, Grep, Glob, Write, Edit, Bash(git log:*), Bash(git tag:*), Bash(ls:*)
---

# Plan the bring-up

One file per revision built: `bringup/rev-a.md` for `releases/rev-a/`. It is a
plan before the boards arrive and a record after. Work from the released
design (`releases/<rev>/` and the tag), not from whatever `main` holds now.

## Sources

Every expected value cites where it came from. If none of these gives one,
write `expected: unknown` and ask, never a guess.

- `docs/SPEC.md` for rails, budgets and limits
- `docs/PINOUT.md` for test points, the programming port and pin functions
- `docs/BOM.md` and the datasheets in `research/parts/` for part limits
- `docs/SUBSYSTEMS.md` for the order to bring subsystems up in

## The plan

```markdown
# Rev A bring-up

Board serial: ____   Date: ____   By: ____

## 1. Before power

- [ ] Visual: solder bridges, missing parts, lifted pins
- [ ] Polarity and pin 1: <each diode, polarized capacitor, IC and connector, by reference>
- [ ] Resistance to ground, unpowered:

| Rail | Test point | Expected | Source | Measured | Pass |
| ---- | ---------- | -------- | ------ | -------- | ---- |

## 2. First power

Bench supply at <the lowest input in the spec's range>, current limit
<expected idle current x 1.5, from SPEC>.

| Rail | Test point | Expected | Tolerance | Source | Measured | Pass |
| ---- | ---------- | -------- | --------- | ------ | -------- | ---- |

- [ ] Input current at idle: expected ____, measured ____
- [ ] No part hot to the touch after one minute

## 3. Subsystems

One section per subsystem in SUBSYSTEMS.md order: clocks and reset, then the
programming port, then each interface, each with what to do, what to expect and
a place for the result.

## Results

What failed, what was reworked by hand, and what goes into the next revision.
```

Order matters: nothing in section 2 before every check in section 1 passes, and
a rail out of tolerance stops everything downstream of it.

## After the measurements

- Each failure becomes a review note or a `/change-design` request for the next
  revision, citing this file. The released revision is never edited.
- Set the release's row in `releases/README.md` to `bring-up passed` or
  `bring-up failed`, and the README's State to Phase `bring-up`.

Commit with a message like `bringup: record rev A first power`.
