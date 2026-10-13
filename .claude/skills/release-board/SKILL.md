---
name: release-board
description: Freeze one revision of the board for fabrication in releases/rev-<letter>/ (gerbers, drill and placement files from KiCad, the supplier BOM, the design at that commit and a README that says what was and was not verified) and tag it. Never orders boards or sends files anywhere. Run it only when the person asks to release, for example /release-board, "make a release", "prepare the gerbers for JLCPCB" or "freeze rev A".
disable-model-invocation: true
argument-hint: "[rev-letter] [supplier: jlcpcb | digikey | mouser]"
allowed-tools: Read, Grep, Glob, Write, Edit, Bash(git status:*), Bash(git log:*), Bash(git rev-parse:*), Bash(git tag:*), Bash(copperhead check:*), Bash(copperhead --json check:*), Bash(copperhead --version), Bash(copperhead export bom:*), Bash(kicad-cli:*), Bash(ls:*), Bash(mkdir:*), Bash(cp:*), Bash(sha256sum:*), Bash(find:*)
---

# Release the board

A release is the set of files a fab receives, frozen at one commit. It is never
changed afterwards: a different board is a new revision. Nothing here uploads,
orders or sends anything; that stays with the person.

## 1. Name the revision

The first release is `rev-a`, then `rev-b`, and so on, after the last folder in
`releases/`. Use the person's name for it if they gave one. The folder and the
git tag share the name.

## 2. Refuse what is not a board

Stop and say why if any of these holds:

- **The tree is not clean.** A release is built from a commit, not from
  uncommitted edits.
- **Connections are unrouted.** A first build leaves ratsnest on purpose.
  Count them:

  ```bash
  mkdir -p .copperhead/runs
  kicad-cli pcb drc --schematic-parity --format json --output .copperhead/runs/release-drc.json <name>.kicad_pcb
  ```

  Any `unconnected_items` means the board would not work. Route them first
  (`/change-design`, or by hand in KiCad).
- **`copperhead check` reports errors** the person has not accepted one by
  one. Each accepted finding goes into the release's Known issues.

## 3. Write the files from the commit

```bash
REV=rev-a; REL=releases/$REV; B=<name>      # B: the .kicad_pro's name
mkdir -p $REL/fabrication $REL/bom $REL/design

INNER=$(grep -oE '"In[0-9]+\.Cu"' $B.kicad_pcb | tr -d '"' | sort -uV | paste -sd, -)
kicad-cli pcb export gerbers --output $REL/fabrication/ \
  --layers "F.Cu,${INNER:+$INNER,}B.Cu,F.Paste,B.Paste,F.Silkscreen,B.Silkscreen,F.Mask,B.Mask,Edge.Cuts" $B.kicad_pcb
kicad-cli pcb export drill --output $REL/fabrication/ $B.kicad_pcb
kicad-cli pcb export pos --format csv --units mm --side both --output $REL/fabrication/$B-placement.csv $B.kicad_pcb

copperhead export bom --supplier jlcpcb
cp outputs/jlcpcb-bom.csv $REL/bom/

cp $B.kicad_pro $B.kicad_sch $B.kicad_pcb $REL/design/
kicad-cli sch export pdf --output $REL/design/$B-schematic.pdf $B.kicad_sch
kicad-cli pcb export step --output $REL/design/$B.step $B.kicad_pcb
```

Use the supplier the person named. `copperhead export bom` refuses when the BOM
has drifted from the design, and leaves out rows marked UNVERIFIED; list every
row it left out in Known issues rather than adding the flag that includes
them.

## 4. Write the release README

`releases/<rev>/README.md`, in this shape:

```markdown
# Rev A

| Field      | Value                                   |
| ---------- | --------------------------------------- |
| Date       | yyyy-mm-dd                              |
| Commit     | <full sha>                              |
| KiCad      | <kicad-cli version>                     |
| copperhead | <copperhead --version>                  |
| Board      | <width> x <height> mm, <n> copper layers |
| Supplier   | <the BOM's supplier>                    |

## Checked

What actually ran on this commit, with its result: copperhead check, DRC with
schematic parity, unrouted count.

## Not checked

Everything a reader might assume was checked and was not: footprints against
datasheets, signal or power integrity, thermal, the gerbers in a viewer,
the fab's own DFM report. Until bring-up passes, the board is unverified.

## Known issues

Each accepted finding and each BOM row left out, with why it was accepted.

## Files

The SHA-256 of every file in this folder except this README.
```

Fill **Files** from:

```bash
(cd $REL && find . -type f ! -name README.md | sort | xargs sha256sum)
```

## 5. Commit and tag

```bash
git add $REL
git commit -m "release: rev A"
git tag -a $REV -m "Rev A"
```

Add a row to the table in `releases/README.md` with the status `not built`, and
set the README's State to Phase `released` and Revision `rev-a`. The tag stays
local: in a workspace without GitHub, only `main` is kept as the project's
history, and pushing a tag to GitHub is the person's call.

Then say what the person still has to do before ordering: open the gerbers in a
viewer, run the fab's DFM check and confirm stock for every BOM row.
