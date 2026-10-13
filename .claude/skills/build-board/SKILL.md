---
name: build-board
description: Build the board from docs/BRIEF.md with copperhead create (spec, architecture, parts, schematic, first layout, outputs, firmware, development plan) and report each stage from the run record. Takes hours and uses model allowance, so run it only when the person asks to build or rebuild from the brief, or for /build-board.
disable-model-invocation: true
allowed-tools: Read, Grep, Glob, Bash(git status:*), Bash(git log:*), Bash(git show:*), Bash(ls:*), Bash(tail:*), Bash(copperhead check:*), Bash(copperhead --json check:*), Bash(copperhead doctor:*), Bash(copperhead --version)
---

# Build the board

`copperhead create` runs eight stages, each of which commits its work:

| Stage            | Writes                                                        |
| ---------------- | ------------------------------------------------------------- |
| `spec-seed`      | `docs/SPEC.md`, the constraint registry, `openspec/specs/`    |
| `architecture`   | `docs/SUBSYSTEMS.md`                                          |
| `part-selection` | `docs/BOM.md`                                                 |
| `schematic`      | `schematic.intent.json`, the schematic, `docs/PINOUT.md`      |
| `layout-draft`   | the board, placed and partly routed; `docs/LAYOUT.md`         |
| `outputs`        | the fabrication package in `outputs/`                         |
| `firmware`       | `firmware/`, with `src/pins.h` from the pinout                 |
| `devplan`        | `docs/DEVPLAN.md`                                             |

The layout stage leaves connections unrouted on purpose and says how many in
`docs/LAYOUT.md`. A finished build is a reviewed draft, not a board to order.

## 1. Before starting

- **The brief is ready.** Run the ready check from `/refine-brief`. If an open
  question blocks the schematic, stop and say which.
- **The tree is clean.** `git status --porcelain` must print nothing. Ask
  before committing the person's work for them.
- **The tools answer.** `copperhead doctor --model claude-code` must pass.
  Relay any failed check as it is printed.
- **The person agrees to the cost.** Say that it takes hours, uses the
  project's model allowance and commits after every stage.

## 2. Start it in the background

In a copperhead workspace the model is `claude-code`: copperhead drives Claude
Code, which reaches the model through the workspace's own connection. A
`compat:` model answers 404 there.

```bash
mkdir -p .copperhead/runs
copperhead --plain create --brief docs/BRIEF.md --model claude-code > .copperhead/runs/create.log 2>&1
```

Run it with the Bash tool in the background (or under `nohup ... &` in a
terminal), so this conversation stays usable. `.copperhead/runs/` is ignored by
git, and the log never belongs in the repository.

## 3. Follow it

- **Stage progress** is `git log --oneline`: each stage commits.
- **What is happening now** is `tail -n 40 .copperhead/runs/create.log`.
- **Never read a transcript whole.** Each stage's transcript under
  `.copperhead/runs/<time>/` can run to hundreds of thousands of tokens. Search
  it for the stage's verdict or the failing tool call.

Report at each stage boundary in one line: the stage, done or failed, and what
it wrote.

## 4. Judge the result by its record

The exit status, the stage commits and `copperhead check` afterwards are the
result. What the model wrote about its own work is not.

- **Exit 0:** run `/check-design`, set Phase to `review` in the README, and
  suggest opening the board in the viewer.
- **A stage failed:** name the mechanism from the log (a missing footprint
  library, a refused part, a gate that did not hold), not "it failed". Do not
  repair the design by hand. Running the same command again resumes: stages
  whose result already holds are skipped. Change one thing between attempts,
  and say which.
- **It stopped to ask:** a stage that needs the person (for example a
  footprint that is not installed) prints what it needs. Relay it as it is.
