---
name: review-notes
description: Work through the open notes left on the board in the viewer under .copperhead/review/, answering questions, turning improvements into copperhead changes and resolving each one. Use for /review-notes, "answer my notes", "what did I flag on the board", or when the person mentions a note on the schematic or board.
argument-hint: "[note id]"
allowed-tools: Read, Grep, Glob, Edit, Bash(git status:*), Bash(git log:*), Bash(sha256sum:*), Bash(ls:*)
---

# Review notes

The board viewer saves each note as one Markdown file in `.copperhead/review/`,
named `<yyyymmdd>-<slug>-<6 characters>.md`: YAML front matter, then the note.

```yaml
---
kind: question          # question | improvement | note
status: open            # open | resolved
file: board.kicad_pcb   # the design file, from the repository root
view: board (F.Cu, B.Cu)  # or: schematic page 1 (Power)
anchors:
  - uuid: 4f1c...       # the KiCad object, by uuid
    label: U3           # what it was called when the note was made
region: [x0, y0, x1, y1]  # present when the note marks an area
sha256: 9ab2...         # the design file's hash when the note was made
author: ...
created: 2026-10-14T09:12:00Z
---

Why is the 3V3 regulator this far from the MCU?
```

## 1. Find the open ones

```bash
grep -l '^status: open' .copperhead/review/*.md
```

With an id argument, take only that note. Otherwise go through them oldest
first, questions before improvements.

## 2. Check the note still points at something

Compare the note's `sha256` with `sha256sum <file>`. If they differ, the design
changed since the note was made: find each anchor's `uuid` in the file and say
which are gone. A note whose objects are gone is answered against what replaced
them, or resolved with the person's agreement.

## 3. Act by kind

- **question:** answer in the conversation with evidence: references, nets,
  the datasheet or the document line that settles it. Ask whether the answer
  closes it.
- **improvement:** turn it into one `/change-design` request that cites the
  note's id. Resolve it after that change commits, not before.
- **note:** acknowledge it. Resolve it only when the person says so.

## 4. Resolve

Change only the `status:` line to `status: resolved`. Leave the body, the
anchors and every other key as they are: the viewer owns the format, and a note
anchored by uuid must not be moved. Commit with a message that names the
notes, for example `review: resolve the regulator placement question`.
