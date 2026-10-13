# Working in this repository

This file is for an agent working in the repository beside copperhead, such as
Claude Code. If you are copperhead's own pipeline, running inside
`copperhead create` or `copperhead do`, follow your stage instructions; this
file does not apply to you.

This repository is one printed circuit board: the brief, the research behind
it, the KiCad design and every revision released for fabrication. It is not a
software project. Read `README.md` first: its State table says where the board
stands, and its Layout says where everything lives.

## Where things live

- **What is being built** is `docs/BRIEF.md`. It may have been drafted in a
  conversation and be incomplete. It is the one source of what is being built:
  when the person changes their mind, the brief changes first.
- **The design** is the KiCad project at the top level (`<name>.kicad_pro`,
  `.kicad_sch`, `.kicad_pcb`), with `schematic.intent.json` (the netlist the
  schematic is drawn from) and `openspec/` (one requirement file per
  capability). None of it exists until the first build.
- **The design record** is `docs/`: SPEC, SUBSYSTEMS, BOM, PINOUT, DECISIONS,
  CHANGELOG, LAYOUT and DEVPLAN, each kept in step with the board by
  copperhead. Every Markdown file in `docs/` goes into every copperhead run.
- **The evidence for a choice** is `research/`: `parts/` holds one note per
  part decision, `references/` holds reference designs and application notes.
- **Notes left on the board** in the viewer are `.copperhead/review/`, one
  Markdown file per note.
- **What was sent to a fab** is `releases/rev-<letter>/`, and what was measured
  when it came back is `bringup/rev-<letter>.md`.

## Rules

1. **Never edit `.kicad_sch`, `.kicad_pcb`, `.kicad_pro`,
   `schematic.intent.json`, `sym-lib-table` or the files copperhead keeps in
   `docs/` with your own file tools.** Change them with `copperhead do` (see
   `/change-design`). The one file in `docs/` you edit is `BRIEF.md`.
2. **Run copperhead with `--model claude-code` in a copperhead workspace.** The
   workspace reaches models only through Claude Code's own connection; a
   `compat:` model answers 404 there.
3. **Never invent a requirement.** If the brief does not say it, ask, or write
   it under Assumptions in the brief with what happens if it is wrong.
4. **Trace every choice.** A requirement cites the brief. A part choice cites
   its note in `research/parts/`. A design change cites the requirement it
   serves.
5. **A datasheet beats a listing, and a date beats neither.** Record where a
   number came from and the day you read it. A vendor claim stays a claim
   until a datasheet or a measurement confirms it.
6. **Judge a run by its record, not its narration.** A copperhead run's result
   is its exit status, its JSON outcome and `copperhead check` afterwards, not
   what the model said it did.
7. **Do not read run transcripts whole.** `.copperhead/runs/` holds full
   transcripts that run to hundreds of thousands of tokens. Search them for
   what you need. They are ignored by git and never committed.
8. **`docs/` holds the design record and nothing else.** Put notes, drafts and
   research anywhere else; a file in `docs/` is read by every run.
9. **`outputs/` is written by copperhead only**, and a release folder is never
   changed after its commit. A different board is a new revision.
10. **Nothing confidential goes in the repository.** Everyone who can open the
    project reads it.

## Commits

Commit each finished piece of work with a message that says what changed, for
example `docs: add the battery charger to the brief`. A copperhead run
commits its own changes. In a copperhead workspace without GitHub, every
commit on `main` is kept as the project's history as soon as it is made; there
is nothing to push by hand.
