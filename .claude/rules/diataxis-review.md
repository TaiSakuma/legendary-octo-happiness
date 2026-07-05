# Diátaxis review

How `README.md` sections are classified and reviewed, following
[Diátaxis](https://diataxis.fr/): documentation divides into four quadrants
by what the reader is doing. Each quadrant has a reader question a reviewer
must be able to answer after reading, and a visible marker that declares it
in a section's heading.

| Quadrant    | Marker | Orientation       | Reader question                                        |
| ----------- | ------ | ----------------- | ------------------------------------------------------ |
| tutorial    | 🎓     | learning by doing | "Did I learn something by doing it?"                   |
| how-to      | 🔧     | achieving a task  | "Following the steps, did I accomplish the task?"      |
| reference   | 📋     | looking up facts  | "Could I find the fact, and can I trust it?"           |
| explanation | 📖     | understanding     | "After reading, do I understand how and why it works?" |

## Rules

- Every section belongs to exactly one quadrant, **declared by the visible
  marker in its heading** (legend above; the README explains the markers to
  its readers in one line). A heading whose subsections span quadrants is a
  **container**: it carries no marker and at most one orientation sentence
  of its own, and each subsection declares its own quadrant.
- Merging is a same-quadrant operation: the merged section carries exactly
  one declaration, so sections of different quadrants cannot merge —
  imported cross-quadrant content would be bleed by construction. Related
  sections of different quadrants are **grouped** instead, as subsections
  under a container heading, each keeping its own declaration.
- Splitting is likewise same-quadrant: the products of a split inherit the
  original section's declaration. A split never assigns new quadrants —
  content that belongs in another quadrant leaves through bleed relocation
  (possibly into a section newly added in that quadrant), and what remains
  keeps its declaration. An outcome that spans quadrants is a net result of
  relocation plus add, not a single split. Bringing a legacy mixed section
  under these rules (assigning its target structure) is a scoping decision,
  like reclassification.
- Content sitting in a section whose quadrant it does not match is
  **bleed**: relocate it to the section that owns that quadrant; do not
  polish it in place. A `write-readme` run relocates bleed itself, creating
  or removing sections as the content requires; the `review-readme` audit
  routes such findings to `write-readme` instead of restructuring during a
  patch pass.
- A reviewer ask that would pull a section toward a quadrant it does not
  target is **out of scope** for that section: route it to the owning
  section, never report it as a defect.
- The declared quadrant is the **fixed point** of a review round: reviewers
  judge content against the declaration, never the reverse. Reviewer
  remedies move content — revise it, relocate it, add, split, merge, or
  remove sections. Changing a declaration (reclassification) is a scoping
  decision made when a run is scoped or by the user, not a review outcome.
- Reviewer self-check, before reporting: answer the declared quadrant's
  reader question; list the asks you routed as out of scope and where they
  went; flag any bleed you found on each section.

## Markers as the declaration record

The markers in the shipped `README.md` are the authoritative section →
quadrant record; there is no separate table to keep in sync. During a
`write-readme` run, each draft declares its own structure the same way —
every draft heading carries its marker — so the working declarations travel
with the text under review, and the review brief carries each section's
status and the reader questions.

A section whose heading has no marker and has body content of its own
(currently "Release process") is **legacy**: it predates these rules, and
its target structure is assigned when its revision is scoped. An unmarked
heading with marked subsections and no body of its own is a container, not
legacy. The `review-readme` audit flags unmarked non-container sections,
markers outside the legend, and content that does not match its marker.

The tutorial quadrant is currently unused; it becomes relevant only if the
README gains a learn-by-doing walkthrough.
