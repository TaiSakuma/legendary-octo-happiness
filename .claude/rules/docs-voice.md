---
paths:
  - "README.md"
---

# README Voice

The audience is international developers — Python and CI users reading a
reference implementation in order to adopt it. These rules apply to
`README.md` prose, not to code, workflow comments, scratch notes, or commit
and pull-request text.

## Voice

- Avoid slang.
- Avoid idioms and figures of speech that mainly native English speakers use
  (no sports, military, or cooking metaphors).
- Prefer literal expressions over figurative ones ("found the bug", not
  "hunted down the bug").
- Established technical terms are fine even when metaphorical ("trigger",
  "push", "squash", "pipeline"); the idiom and figurative-language rules
  target decorative figures of speech, not standard vocabulary.
- Prefer precise words: exact file names, tag patterns, settings labels —
  not "the config" or "some settings". But do not invent precision: avoid
  literal values that go stale when a stable reference exists (write "the
  newest release", not today's version number, unless it is an example).
- State things directly — no hedging. Expressing calibrated uncertainty is
  information, not hedging; do not upgrade uncertain claims to fact.
- Spell out each acronym on first use, then use the short form. Skip
  expansions the audience certainly knows better than the long form (URL,
  API, CI, PR).
- Explain ideas in your own words rather than lightly rewording a source
  (the Conventional Commits specification, git-cliff docs, GitHub docs).
- Put reused external wording in quotation marks, attribute it, and link the
  source; never present quoted phrasing as your own.
- Verbatim reproduction is correct when exact wording is the point: GitHub
  UI settings labels ("Pull request title and description"), tag patterns,
  commands, workflow and action names. Quote or fence them exactly rather
  than paraphrase — a paraphrased UI label is a defect, not a style choice.

## Conventions

- Reference-style links (`[text][label]`, definitions grouped near the
  section), matching the existing file.
- Hard wrap prose at ~80 columns; keep tables pipe-aligned.
- Fenced code blocks specify a language (MD040).
- Give a concrete example alongside every pattern (`u1.2.3` for `u` +
  version), and keep placeholder styles consistent within a section.
- Steps in how-to sections name their actor ("User ...", "GitHub Actions
  ...").
- Section headings carry their Diátaxis quadrant marker (legend in
  `.claude/rules/diataxis-review.md`); container headings and document
  front matter carry none.

## During persona review

Persona-suggested wording is advisory: the orchestrator writes the final
text itself, following these rules. A persona ask that conflicts with this
file is resolved in favor of this file unless the user rules otherwise.
