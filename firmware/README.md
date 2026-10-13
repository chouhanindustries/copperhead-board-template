# Firmware

Code that runs on the board, when it runs any.

`src/pins.h` is generated from `docs/PINOUT.md` by the build's firmware stage,
and `PINOUT.md` stays the one source of truth: a pin moves in the design first.
`copperhead sync --dry-run` reports a define in `pins.h` that `PINOUT.md` does
not mention, and `copperhead sync` regenerates the header.

Everything else here is yours: the toolchain, the build and the tests. Keep
build output out of git.
