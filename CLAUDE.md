@AGENTS.md

## Skills

The skills in `.claude/skills/` follow a board from brief to bring-up, in this
order:

| Skill             | Use it to                                                             |
| ----------------- | --------------------------------------------------------------------- |
| `/refine-brief`   | review `docs/BRIEF.md` with the person until a build can start from it |
| `/research-part`  | choose a part for one function and record why in `research/parts/`    |
| `/build-board`    | run the first build from the brief with `copperhead create`           |
| `/change-design`  | change the schematic, board or parts through `copperhead do`          |
| `/check-design`   | report ERC, DRC and drift findings, one row each                      |
| `/review-notes`   | answer and resolve the notes left on the board in the viewer          |
| `/release-board`  | freeze a revision for fabrication in `releases/`                       |
| `/plan-bringup`   | write the power-on plan for a revision and record what was measured   |
| `/project-status` | say where the board stands and update the State table                 |

`/build-board` and `/release-board` run only when the person asks for them:
one takes hours of model time, the other creates a revision that is never
changed.
