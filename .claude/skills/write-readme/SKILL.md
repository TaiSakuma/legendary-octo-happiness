---
name: write-readme
description:
  Author or substantially revise README.md via the persona-review workflow
---

Author (or substantially revise) `README.md` using the persona-review
workflow adapted from hypothesis-awkward's `write-docs-page` skill. There
the unit of work is one page and the site grows page by page; here the unit
of work is the whole document. A revision may be triggered by one feature or
one weak section, but drafting, review, and shipping cover the README as a
whole, and the **section set is an output of the run**: sections are added,
split, merged, and removed as the content requires. The goal is a document
whose sections each serve their primary personas and are accurate;
**accuracy beats style**.

The five review personas are the `readme-persona-*` subagents in
`.claude/agents/`: `adopter`, `release-operator`, `ci-expert`, `evaluator`,
and `ai`.

Every section of the document is declared in a **quadrant** and a
**status**:

- Quadrant — the section's [Diátaxis](https://diataxis.fr/) quadrant
  (tutorial, how-to, reference, or explanation), declared by the visible
  marker in the section's heading; `.claude/rules/diataxis-review.md` holds
  the marker legend, the one-quadrant-per-section rule, and the
  bleed/relocation rules.
- Status — *implemented* (describes current behavior; source of truth is the
  workflow files and actions) or *spec* (describes intended behavior; source
  of truth is a design brief supplied when the skill is invoked — a decision
  list or note that need not live in the repository). Spec status is how this
  repository practices README-driven development: the feature is settled in
  prose first, and the settled text binds the implementation that follows.

## Steps

1. **Scope** — Confirm the change driving the revision and its status
   (implemented or spec), the personas it primarily serves (whose verdicts
   outweigh the others when fixes conflict), and what is out of scope.
   Sketch the target section set — starting from the heading markers in the
   shipped README, adding, splitting, merging, or removing sections as the
   content requires — and assign each section a quadrant marker. For spec
   status, capture the design decisions the text must encode into the
   review brief (step 5) so the brief is self-contained.

2. **Gather sources** — Collect the raw material: `.github/workflows/*`, the
   marketplace actions' `action.yml` (fetch the currently pinned versions),
   `CONTRIBUTING.md`, `.claude/CLAUDE.md`, the supplied design brief for spec
   content, and the repository settings the workflows assume.

3. **Rubric** — Itemize what the document must say (Content), must be true
   (Accuracy), must exclude (Exclusions), and must satisfy editorially
   (`.claude/rules/docs-voice.md`). For spec content, pin the feature
   semantics first — the design decisions the text must encode. All three
   drafts inherit the semantics, so a wrong decision poisons them
   identically and the persona pass will not reliably catch it; settle
   semantics against the design brief before drafting.

4. **Diverse drafts** — Write three structurally distinct whole-README
   drafts, all meeting the rubric. Section structure is part of the
   variation: drafts may differ in how many sections exist and how content
   is distributed among them, as long as each draft keeps every section in
   exactly one quadrant. Write the drafts to scratchpad files; each draft
   declares its own structure through the markers in its headings.

5. **Parallel persona review** — Launch the five persona subagents in
   parallel via the Agent tool's `subagent_type`. Write a shared review brief
   (the document's purpose; each section's status and reader question —
   quadrants are declared by the drafts' own heading markers; what is in
   and out of scope; rubric; verified facts; link targets) to a temp file
   and pass each subagent its path plus the draft paths. For spec content the brief itself carries the
   design decisions — subagents never depend on files outside the repository
   and the brief. Ask each for: answers to the reader questions for the
   sections its lens serves; a score per draft on the rubric axes;
   lens-specific flags with quoted text and `file:line` citations; how
   relevant each section is to it; the best draft overall and per axis;
   specific fixes; structural recommendations (sections to add, split,
   merge, or remove); the single most important improvement; an alignment
   self-check (relocations and bleed flagged — see
   `.claude/rules/diataxis-review.md`); and a one-line ship/revise verdict
   (with the single most important change if revising). Consolidate into a
   matrix. If a reviewer errors out mid-run, re-launch it — do not treat a
   missing verdict as a pass.

6. **Fact-check** — For implemented content, verify every claim against the
   actual workflow files, the actions' inputs/outputs, and the repository
   settings, and confirm every link resolves. For spec content, verify every
   claim against the design decisions in the brief, and verify
   implementability: a behavior GitHub Actions cannot deliver as written is a
   blocking defect. Accuracy beats style.

7. **Synthesize or select** — First, if the review surfaced a flaw shared by
   all three drafts (most often wrong or missing semantics), fix it across
   the drafts and re-review once or twice before proceeding. Then produce the
   document: when strengths are split across drafts, merge the per-axis
   winners; when one draft is strongest on most axes, take it as the base and
   graft only the specific wins from the others. Merging adds seams, so do
   not merge for its own sake. Write the final text yourself, following
   `.claude/rules/docs-voice.md` — persona-suggested wording is advisory.

8. **Re-review the resulting README** — The draft review does not cover the
   text you will ship: a merge can inherit a weakness shared by all drafts,
   and an edited draft carries changes no reviewer saw. Run the personas
   again on the resulting document (same brief; the resulting README's own
   heading markers carry the current declarations, however many sections a
   round has added, split, merged, or removed), apply the genuine fixes,
   and re-review — iterating until all five return a "ship" verdict (cap at
   five rounds). Re-run the
   mechanical checks each round, since a fix can introduce a new error. If
   the cap is reached with dissent remaining, stop and present the
   unresolved verdicts to the user — do not keep bending the text to chase
   the last holdout.

9. **Verify mechanically** — Run the `/review-readme` audit: local links
   resolve, and the workflows table matches the actual workflow files and
   their triggers. Check the text against the conventions in
   `.claude/rules/docs-voice.md` (MD040 fences, aligned tables, ~80-column
   wrap, reference-style links, examples alongside patterns).

10. **Record** — List every section-set change (added, split, merged,
    removed, reclassified) in the report; the shipped README's heading
    markers are themselves the declaration record, so there is no table to
    sync. For spec content, list the claims that describe intended behavior
    in the implementation plan, so each is re-verified against the real
    workflows after the implementation ships.

## Guidelines

- `README.md` is the only document: a section is written well when its
  primary personas find what they need and the others can tell early that it
  is not for them while still seeing it is useful to its own readers.
- A section is not obligated to serve every persona, and the document does
  not owe any persona a section. The correct review from a low-relevance
  persona is a low relevance score and a ship verdict — not asks that bend
  the document toward its lens. When personas' fixes conflict, the primary
  personas from step 1 win.
- The section set is an output of the run: relocating bleed, creating the
  section a quadrant needs, and removing a section that no longer serves
  anyone are actions the run takes, guided by persona feedback. Removal has
  exactly two legitimate sources: a persona speaking as the section's own
  audience (duplication, void purpose, vanished subject), or the
  consolidated matrix showing a section every persona finds low-relevance —
  the latter is the orchestrator's judgment at synthesis, never a single
  low-relevance persona's ask. Every section-set change is listed in the
  report; an ask the run chooses not to serve is reported with a keep/drop
  recommendation for the user.
- Reclassification — changing a section's declared quadrant while keeping
  its content — is a scoping decision (step 1, or the user's call between
  runs), never a review-round outcome. The declaration is the fixed point a
  round reviews against; reviewer remedies move content, not declarations.
  A section that drains through relocations and disappears while a similar
  heading grows elsewhere may look reclassified in the end, and a section
  that sheds bleed into a newly added section of another quadrant may look
  split across quadrants; in both cases the net result is composed of
  relocation plus add or remove — no declaration moved and no single step
  crossed a quadrant.
- In spec status the design decisions in the brief are settled. A persona
  ask that would change a decision is design feedback: surface it in the
  report for the user to rule on; never fold it into the text as if settled.
- Every section belongs to exactly one Diátaxis quadrant; a heading whose
  subsections span quadrants is an unmarked container with at most an
  orientation sentence of its own. Bleed is relocated, not polished in
  place (see `.claude/rules/diataxis-review.md`).
- The actor of every step must be explicit — "User" or "GitHub Actions" —
  in how-to sections; the release-operator persona enforces this.
- A spec section binds the implementation. After the implementation ships,
  a difference between behavior and the section is either an implementation
  bug or a change that re-enters this skill — never a silent doc drift.
- Keep `/review-readme` as the maintenance audit; this skill is for
  authoring and substantial revision.
- Voice and formatting follow `.claude/rules/docs-voice.md`; the orchestrator
  writes the final text, not the personas.
