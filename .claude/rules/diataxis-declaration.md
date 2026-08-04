# Diátaxis declaration

How units of content declare their Diátaxis quadrants in `README.md`, the
repository's document. The review rules that use these declarations are in
the `reviewed-writer` plugin's `persona-review` skill
(`references/diataxis-review.md`, the shared core); this file supplies the
marker legend, the section shapes, and the declaration record the core
defers to.

## Markers

A unit of content is a README section. It declares its quadrant with a visible
marker in its heading; the markers are deliberately not explained in the README
itself:

| Quadrant    | Marker |
| ----------- | ------ |
| tutorial    | 🎓     |
| how-to      | 🔧     |
| reference   | 📋     |
| explanation | 📖     |

In the core's declaration pass, a well-formed marker sits in the section's
heading with a value from this legend. The tutorial quadrant is currently
unused; it becomes relevant only if the README gains a learn-by-doing
walkthrough.

## Section shapes

A heading whose subsections span quadrants is a **container**: it carries no
marker and at most one orientation sentence of its own, and each subsection
declares its own quadrant.

A section whose heading has no marker and has body content of its own
(currently "Release process") is **legacy**: it predates these rules, and its
target structure is assigned when its revision is scoped — a scoping decision,
like reclassification. An unmarked heading with marked subsections and no body
of its own is a container, not legacy. Legacy sections are what the core's
declaration pass reports as units with no marker.

## Record

The markers in the shipped `README.md` are the authoritative section →
quadrant record; there is no separate table to keep in sync. During a
`write-readme` run, each draft declares its own structure the same way —
every draft heading carries its marker — so the working declarations travel
with the text under review, and the review brief carries each section's
status and the reader questions.

A `write-readme` run relocates out-of-quadrant content itself, creating or
removing sections as the content requires. A `review-readme` run reviews the
shipped README once through the same panel: the reviewers' declaration pass
flags unmarked non-container sections, markers outside the legend, and content
that does not match its marker, and the report routes sections needing rework
to `write-readme` — a review run never edits the README.
