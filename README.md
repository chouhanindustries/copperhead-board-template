# Board

What this board is and what it must do is in [docs/BRIEF.md](docs/BRIEF.md).
This file says where the board stands and where everything lives. Rename the
heading above to the board's name once the brief has one.

## State

| Field          | Value                     |
| -------------- | ------------------------- |
| Phase          | brief                     |
| Revision       | none released             |
| Last check     | not run                   |
| Open questions | listed in `docs/BRIEF.md` |

Phases, in order: `brief`, `build`, `review`, `released`, `bring-up`. Keep this
table true; it is the first thing anyone reads. `/project-status` recounts it
from the files.

## Layout

```text
README.md            this file: the board's state and this map
AGENTS.md            what a coding agent needs to know here (CLAUDE.md loads it)
LICENSE

docs/                the design record, kept in step with the board by copperhead
  BRIEF.md           what is being built: drafted in a conversation, refined here
  SPEC.md            the specification derived from the brief
research/            what the design rests on
  parts/             one note per part decision, with its sources
  references/        reference designs, application notes, teardowns
firmware/            code that runs on the board, when it runs any
mechanical/          enclosure, board outline and the parts the board mates with
releases/            one folder per revision sent to a fab, never changed after
bringup/             power-on plan and measurements, one file per revision

.copperhead/         copperhead's settings, constraint registry and review notes
.claude/skills/      the skills Claude Code uses in this project
```

The first build adds these. They stay where copperhead and KiCad put them:

```text
<name>.kicad_pro     the KiCad project, schematic and board, at the top level
<name>.kicad_sch
<name>.kicad_pcb
schematic.intent.json  the netlist the schematic is drawn from
openspec/            one requirement file per capability, with scenarios
outputs/             the fabrication package and supplier BOMs
```

## Rules

1. **Change the design through copperhead or KiCad, never as text.** A
   `.kicad_sch` or `.kicad_pcb` edited as text can describe a board that
   cannot be made, and the damage does not show in a diff. `copperhead do`
   verifies every change with ERC and DRC and rolls back what fails.
2. **`docs/` holds the design record and nothing else.** Every Markdown file in
   it goes into every copperhead run, so notes, research and drafts live
   elsewhere.
3. **The brief is the one source of what is being built.** Open questions and
   assumptions are written at the end of it, not left in a conversation.
4. **`outputs/` is written by copperhead only.** Filling it by hand satisfies a
   pipeline gate and invalidates the run.
5. **A release is never changed.** A different board is a new revision.
6. **Nothing confidential.** Everyone who can open this project reads this
   repository: quotes, contracts and prices stay out of it.

In a copperhead workspace without GitHub, every commit on `main` is kept as
the project's history as soon as it is made.

## Working locally

The CLI needs no account and no cloud:

```bash
npm i -g copperhead
copperhead check                             # ERC, DRC and doc drift; no model, no network
copperhead create --brief docs/BRIEF.md      # the first build, from the brief
copperhead do "..."                          # propose, edit, verify, commit
```

The template is licensed MIT, so a board built from it carries no obligation
from the template itself.
