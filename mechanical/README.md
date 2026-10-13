# Mechanical

What the board has to fit: the enclosure, the board outline as a drawing, and
models of the parts it mates with.

```text
outline.dxf       the board outline and mounting holes, when they come from
                  the enclosure rather than the other way round
enclosure/        enclosure source files and their STEP exports
mating/           STEP models of connectors, displays and other parts the
                  board meets, with where each one came from
```

When the outline comes from here, say so in the brief's Physical section, so a
build draws the board to it.
