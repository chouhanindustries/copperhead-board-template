---
name: refine-brief
description: Review docs/BRIEF.md with the person and refine it until a build can start from it, covering purpose, parts, power, interfaces, physical limits, must-nots, open questions and assumptions. Use when a workspace opens on a new brief, when the person says what they want has changed, or for /refine-brief.
argument-hint: "[what changed, if anything]"
allowed-tools: Read, Grep, Glob, Edit, Write, AskUserQuestion, Bash(git status:*), Bash(git diff:*), Bash(git log:*)
---

# Refine the brief

The goal is a brief that a build can start from without guessing. The brief is
the one source of what is being built, so everything the person decides ends
up in `docs/BRIEF.md`, not only in this conversation.

## 1. Read what is there

Read `docs/BRIEF.md`, the State table in `README.md` and anything in
`research/`. A brief drafted in a copperhead conversation is a `# title` line
and prose; a refined one has the sections of the template (Purpose, Parts,
Power, Interfaces, Physical, Must not, Open questions, Assumptions).

If the board already has a schematic, say so before changing anything: a
changed brief after the first build means a design change (`/change-design`)
or a new build, and the person should choose which.

## 2. Sort it, keeping the person's words

Move each statement into its section. Do not add requirements, round numbers
or upgrade "about 5 V" to "5 V ±5%". Where the brief is vague, the vagueness
becomes a question, not a guess.

## 3. Find what a build needs and the brief lacks

Check each of these, and keep only the ones that are missing:

| Missing                                          | Why a build needs it                          |
| ------------------------------------------------ | --------------------------------------------- |
| input source and voltage range                   | sets the regulators and the protection         |
| current per rail, peak and average               | sizes regulators, traces and thermal relief    |
| every connector and what is on the other end     | fixes pinouts, ESD and the board edge          |
| board size, outline or enclosure                 | bounds placement                               |
| layer count and assembly (hand, PCBA, which fab) | picks footprints and design rules              |
| environment (temperature, outdoor, vibration)    | picks part grades and clearances               |
| quantity and target cost per board               | trades parts against each other                |
| what the board must never do                     | becomes a hard check, not a preference         |

## 4. Ask in one pass

Ask with AskUserQuestion, at most four questions per call, the ones that change
the design most first. Offer a sensible default for each and say what it would
cost to change it after the build. Do not ask what the brief already answers.

## 5. Write it down

- Answers go into their sections, in the person's terms.
- Anything still unknown goes under **Open questions**, each with what it
  blocks.
- Anything assumed without the person saying so goes under **Assumptions**,
  each with what changes if it is wrong.
- Keep the `# title` line as the board's name, and give `README.md` the same
  heading.

Commit with a message that says what changed, for example
`docs: refine the brief's power and interfaces`.

## Ready for a build

The brief is ready when all of these hold. Say which do not:

- the purpose fits in one or two sentences
- every power input has a source and a voltage range
- every interface is listed with what it connects to
- the size is given, or there is a reason it is not
- there is at least one must-not
- no open question blocks the schematic

Do not start the build from this skill. When the brief is ready, say that
`/build-board` is next and that it takes hours of model time.
