---
name: review-readme
description:
  Review README.md once with the persona panel and report; do not modify
---

Run a single round of persona review on the shipped `README.md` — the
single-iteration counterpart of `write-readme`: the same panel, one round,
no revision.

## Steps

1. **Review** — Invoke the `reviewed-writer:persona-review` skill on
   `README.md` as it
   stands, with its standalone defaults: all five personas, implemented
   status, no rubric.

2. **Report** — Present the consolidated matrix: each persona's verdict and
   flags, per-section relevance, the reviewers' declaration passes, and the
   fixes they propose. Do not modify the README. Route any section needing
   rework — and any fix worth applying — to the `write-readme` skill; acting
   on the report is the user's decision.
