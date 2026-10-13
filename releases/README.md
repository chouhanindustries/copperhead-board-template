# Releases

One folder per revision sent to a fab, made by `/release-board`. A release is
never changed after its commit: a different board is a new revision, and the
version a fab or a person received is the one that matters later.

```text
rev-a/
  README.md       date, commit, tool versions, what was checked and what was
                  not, known issues, and the SHA-256 of every file
  fabrication/    gerbers, drill files and the placement file, as sent
  bom/            the supplier BOM, as sent
  design/         the KiCad project at the released commit, its schematic as
                  a PDF and the board as a STEP model
```

Each release is also the git tag of the same name (`rev-a`), so the design it
came from can be checked out.

| Revision | Date | Commit | Sent to | Status |
| -------- | ---- | ------ | ------- | ------ |
