---
name: project-status
description: Say where the board stands, counted from the files (brief readiness and open questions, build stages done, last check, open review notes, releases and bring-up), name the next step and update the State table in README.md. Use for /project-status, "where are we", "what is left before we can order boards", or at the start of a session.
allowed-tools: Read, Grep, Glob, Edit, Bash(git log:*), Bash(git status:*), Bash(git tag:*), Bash(git rev-parse:*), Bash(ls:*), Bash(grep:*), Bash(copperhead check:*), Bash(copperhead --json check:*)
---

# Project status

Numbers somebody could check, counted from the repository, never from memory
of earlier conversations.

## Count

| What              | From                                                                      |
| ----------------- | ------------------------------------------------------------------------- |
| brief             | `docs/BRIEF.md`: which template sections are filled, the open questions   |
| build             | which of SPEC, SUBSYSTEMS, BOM, PINOUT, LAYOUT, DEVPLAN exist in `docs/`, whether a `.kicad_sch` and `.kicad_pcb` exist, the stage commits in `git log` |
| a build running   | `.copperhead/runs/create.log` changed in the last few minutes             |
| check             | `copperhead --json check` on HEAD, with the tree's state                  |
| review notes      | `.copperhead/review/*.md` with `status: open`, by kind                    |
| unrouted          | the count in `docs/LAYOUT.md`'s Draft quality section                    |
| releases          | the `releases/rev-*` folders and their tags                               |
| bring-up          | the `bringup/rev-*.md` files and their Results                            |

## Phase

The first that holds, read from the bottom:

- `bring-up`: a `bringup/rev-*.md` has results
- `released`: a `releases/rev-*` folder exists
- `review`: the schematic and the board exist
- `build`: a build is running, or some stages committed and others did not
- `brief`: none of the above

## Report

1. **Three to five lines of hard facts first:** the phase, the revision, the
   last check's counts, open questions and open notes.
2. **Then the detail table** above, filled in.
3. **One next step**, the one that unblocks the most: an open question that
   blocks the schematic, a failed stage, a DRC error, unrouted connections
   before a release.

Then update the State table in `README.md` (Phase, Revision, Last check, Open
questions) if any value changed, and commit it as `docs: update the board's
state`.
