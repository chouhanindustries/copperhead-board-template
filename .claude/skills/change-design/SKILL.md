---
name: change-design
description: Make any change to the schematic, the board, the parts or the design documents through copperhead do, which gates it on a spec, verifies it with ERC and DRC and rolls back what fails. Use whenever a task touches a .kicad_sch, .kicad_pcb, schematic.intent.json, docs/BOM.md, docs/PINOUT.md or another file copperhead keeps, instead of editing it directly, or for /change-design <request>.
argument-hint: "<the change, in plain language>"
allowed-tools: Read, Grep, Glob, Bash(git status:*), Bash(git log:*), Bash(git show:*), Bash(git diff:*), Bash(copperhead check:*), Bash(copperhead --json check:*)
---

# Change the design

KiCad files are s-expressions in which a small textual mistake describes a
board that cannot be made, and the damage does not show in a diff. **Never edit
`.kicad_sch`, `.kicad_pcb`, `.kicad_pro`, `schematic.intent.json`,
`sym-lib-table` or the documents copperhead keeps in `docs/` with your own file
tools.** `copperhead do` does what a text edit cannot:

- the change is gated on a validated OpenSpec proposal before anything is
  edited
- every edit is verified with ERC, and with DRC when the board changed
- a failure is repaired, and if it cannot be, the tree is rolled back
- SPEC, BOM, PINOUT, DECISIONS and CHANGELOG are kept in step with the board

## 1. Write the request

One change per request. Name what it touches by reference and net, say why
(the brief line, requirement or review note it serves) and say how to tell it
worked.

> Replace U3 with a TPS62133 (research/parts/buck-3v3.md) so the 3V3 rail
> carries 2 A peak. Keep the existing input and output capacitors if they fit
> its datasheet; ERC and DRC must stay clean.

## 2. Look before a large change

For a change that touches more than a few parts, a whole subsystem or the
board outline, propose it first and show the person what it would do:

```bash
copperhead --json do "<request>" --model claude-code --dry-run
```

## 3. Run it

```bash
copperhead --json do "<request>" --model claude-code
```

In a copperhead workspace the model is `claude-code`; a `compat:` model answers
404 there. The tree must be clean; commit or set aside the person's own edits
first rather than passing `--allow-dirty`.

## 4. Read the outcome

The JSON's `outcome` is the result, not the model's narration. It also carries
`summary`, `filesTouched`, `commit` and `transcriptDir`.

- **`success`:** the change passed verification and was committed. Report
  `commit`, `filesTouched` and the `summary`. A dry run also ends in `success`
  with no commit; show the person the diff it printed.
- **`refused`:** the pipeline declined. Relay the reason from `summary`; it is
  usually a constraint or a budget the request breaks, and the person decides.
- **`failure`:** verification could not be satisfied and the tree was rolled
  back. Nothing is broken. Say what could not be satisfied, searching the
  transcript in `transcriptDir` (never read it whole), and ask before trying
  another approach. Do not retry the same request, and do not make the edit by
  hand instead.

After a success, run `copperhead check` and report any finding the change
introduced.

## When KiCad is the better tool

Some work is faster by hand in KiCad on a desktop: fine placement, routing,
silkscreen. That is the person's call. After a hand edit, run
`copperhead check` and `copperhead sync --dry-run` so the documents catch up
with the board.
