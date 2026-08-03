# Persona-review profile

Repository-specific values for the persona-review engine in
`.claude/skills/write-readme/SKILL.md`. The engine reads this file first
and refers to its sections by name; the section structure is shared with
the counterpart profile in hypothesis-awkward.

## Document

The document is `README.md`, the repository's only document; the unit of
work is the whole document. The section set is an output of the run.
Provenance: the workflow was adapted from hypothesis-awkward's
`write-docs-page` skill — there the unit of work is one page and the site
grows page by page; here it is the whole document.

## Personas

The five review personas are the `readme-persona-*` subagents in
`.claude/agents/`: `adopter`, `release-operator`, `ci-expert`,
`evaluator`, and `ai`. The primary personas are chosen when the run is
scoped (step 1).

## Declaration mechanism

Each section's Diátaxis quadrant is declared by the visible marker in its
heading. The Diátaxis rules are `.claude/rules/diataxis-review.md`, which
holds the marker legend, the one-quadrant-per-section rule, and the
bleed/relocation rules. Every section belongs to exactly one quadrant;
there are no sanctioned multi-quadrant sections — a heading whose
subsections span quadrants is an unmarked container with at most an
orientation sentence of its own. Declarations travel with the text: each
draft and the shipped README declare their own structure through their
heading markers, and the review brief carries each section's status and
reader question, not its quadrant. Out-of-scope asks and bleed are routed
to the owning section within the README, creating or removing sections as
the content requires.

## Premise to pin

Trigger: spec content. Premise: the feature semantics — the design
decisions the text must encode. Authority: the design brief.

## Sources

`.github/workflows/*`; the marketplace actions' `action.yml` (fetch the
currently pinned versions); `CONTRIBUTING.md`; `.claude/CLAUDE.md`; the
supplied design brief for spec content; and the repository settings the
workflows assume.

## Fact-check targets

For implemented content: the actual workflow files; the actions' inputs
and outputs; the repository settings; and every link (confirm each
resolves). For spec content: the implementability platform is GitHub
Actions.

## Status dimension

Enabled. For implemented content the source of truth is the workflow files
and actions. Spec status is how this repository practices README-driven
development: the feature is settled in prose first, and the settled text
binds the implementation that follows.

## Verification

No one-time wiring. Checks, re-run each review round: the `/review-readme`
audit — local links resolve, and the workflows table matches the actual
workflow files and their triggers — and the voice check against
`.claude/rules/docs-voice.md` (MD040 fences, aligned tables, ~80-column
wrap, reference-style links, examples alongside patterns).

## Record

List every section-set change (added, split, merged, removed,
reclassified) in the report. The shipped README's heading markers are
themselves the declaration record; there is no table to sync.

## Voice rules

`.claude/rules/docs-voice.md`.

## Extra guidelines

- The actor of every step must be explicit — "User" or "GitHub Actions" —
  in how-to sections; the release-operator persona enforces this.
- Reclassification — changing a section's declared quadrant while keeping
  its content — is a scoping decision (step 1, or the user's call between
  runs), never a review-round outcome. The declaration is the fixed point
  a round reviews against; reviewer remedies move content, not
  declarations. A section that drains through relocations and disappears
  while a similar heading grows elsewhere may look reclassified in the
  end, and a section that sheds bleed into a newly added section of
  another quadrant may look split across quadrants; in both cases the net
  result is composed of relocation plus add or remove — no declaration
  moved and no single step crossed a quadrant.
- Keep `/review-readme` as the maintenance audit; this skill is for
  authoring and substantial revision.
