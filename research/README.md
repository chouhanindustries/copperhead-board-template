# Research

What the design rests on. These are inputs, not outputs: the design record
copperhead keeps is in `docs/`.

```text
parts/        one note per part decision: the need, the candidates, the choice
              and its sources. Named for the function, e.g. buck-3v3.md
references/   reference designs, application notes, teardowns and standards,
              each with where it came from and its licence
```

Rules that have cost time before:

- **Record what you checked against a live source, and on what date.** Stock,
  prices and lifecycle status go stale within weeks.
- **A datasheet beats a listing.** Distributor pages and vendor summaries are
  claims until the datasheet or a measurement confirms them.
- **Licence text beats repository metadata.** Read the licence file; hosting
  sites misclassify hardware licences such as CERN-OHL.
- **Say whether a design is reference or reusable.** A design you may read is
  not always one you may ship. Name the licence that decides it.
- **Link large files instead of committing them.** A datasheet PDF is a link
  and a revision; the repository holds the note that says what was read in it.
