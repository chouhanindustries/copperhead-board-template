# Bring-up

One file per revision that came back from the fab, made by `/plan-bringup`:
`rev-a.md` for `releases/rev-a/`.

Each file is a plan first and a record after. Every check names the value it
expects and where that value came from (the spec, the pinout or a datasheet),
and leaves a place for what was measured:

1. Before power: inspection, polarity and resistance from each rail to ground.
2. First power: a current-limited supply, then each rail at its test point.
3. Each subsystem: clocks, reset, the programming port, then each interface.

A failed check becomes a review note or a change for the next revision. The
released revision itself is never edited.
