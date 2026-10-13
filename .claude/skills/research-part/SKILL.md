---
name: research-part
description: Choose a part for one function of the board and record why in research/parts/, comparing candidates on the numbers that matter, reading the datasheet rather than the listing and checking stock and lifecycle. Use for "which regulator should we use", "find a USB-C controller", "is this part still available", or /research-part <function or part number>.
argument-hint: "<function or part number>"
allowed-tools: Read, Grep, Glob, Write, Edit, WebSearch, WebFetch
---

# Research a part

One note per function, in `research/parts/<function>.md`, named for what the
part does (`buck-3v3.md`, `usb-c-sink.md`, `imu.md`), not for the part number,
so the note survives the part being replaced.

## 1. Pin down the need

From `docs/BRIEF.md` (and `docs/SPEC.md` once it exists) write the numbers the
part must meet, each with the line it comes from: voltages, currents,
interfaces, package limits from the assembly method, temperature grade. If a
number the choice depends on is not in the brief, ask for it instead of
picking one.

## 2. Find candidates

Two to four real candidates. For each, read the **datasheet** and record the
numbers that decide this function, with the datasheet revision. Distributor
pages and vendor summaries are claims; where a number comes only from one,
mark it `unconfirmed`.

Check, and date, for each candidate:

- stock at one or two distributors, and the price at the board's quantity
- lifecycle status (active, not recommended for new designs, obsolete)
- whether KiCad's standard libraries have its symbol and footprint

## 3. Write the note

```markdown
# <function>: <chosen part>

Status: chosen | candidate | rejected
Checked: <yyyy-mm-dd>

## Need

What the board needs from this part, each number with the brief line or
requirement it comes from.

## Candidates

| Part | Maker | Deciding numbers | Package | Stock and lifecycle | Notes |
| ---- | ----- | ---------------- | ------- | ------------------- | ----- |

## Choice

Why this one in two or three sentences, and what would make us change it.

## Sources

- datasheet: <url>, revision <rev>, read <date>
- distributor: <url>, read <date>
- reference design: <url>, licence <licence>, reference or reusable
```

Link datasheets; do not commit the PDFs.

## 4. Carry the choice into the design

- **Before the first build:** add the part to the brief's Parts section, with
  a pointer to the note, so the build uses it.
- **After the first build:** a different part is a design change. Hand it to
  `/change-design` with the note as the reason. Never edit `docs/BOM.md` or
  the schematic to swap it.

Commit the note with a message like `research: choose the 3.3 V buck`.
