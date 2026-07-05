---
name: review-readme
description:
  Audit README.md against the current implementation and fix drift
---

Review and update README.md for this repository.

This is the maintenance audit: it verifies the existing README against the
current implementation and patches drift in place. For authoring a new
section or substantially revising one, use the `write-readme` skill instead.

## Steps

1. Read `README.md` in full.

2. **Link audit** — Extract every `[text](path)` link that points to a local file. Verify each target exists using Glob. Report any broken links.

3. **Workflows table** — Compare each row of the "GitHub Actions workflows" table against the actual files in `.github/workflows/`. Verify the file exists, the trigger column matches the workflow's `on:` block, and the purpose column matches what the workflow does. Flag workflows that exist but are missing from the table.

4. **Release process** — Verify the numbered steps in the "Release process" section match the actual behavior of `changelog.yml` and `release.yml`: the trigger tag pattern, the generated `v` tag, and the GitHub Release creation.

5. **GitHub settings** — Check that the repository settings listed in the README are still what the workflows assume (squash merge only, with "Pull request title and description" as the default commit message).

6. **Marketplace actions** — Verify the "Marketplace actions" section lists the composite actions the workflows actually use and that the marketplace links resolve.

7. **Persona and quadrant pass** — Read the README once through each of the five `readme-persona-*` lenses defined in `.claude/agents/` (adopter, release-operator, ci-expert, evaluator, ai) and note the gaps each would hit. This is a quick inline pass, not the full persona review — do not launch the subagents. Also check each section against the quadrant marker in its heading (legend in `.claude/rules/diataxis-review.md`): flag bleed (content not matching the marker), unmarked sections (legacy — but a marker-less container heading whose subsections are marked is not), and markers outside the legend. When this pass shows a section needs substantial rework rather than a patch, route that section to the `write-readme` skill instead of fixing it here.

8. **Report** — Summarize findings before making changes: broken links, stale table rows, mismatches with workflow files, persona gaps, quadrant bleed, and any section routed to `write-readme`.

9. **Apply fixes** — Update README.md to address all findings that are patches (not routed rework). Follow the README rules in CLAUDE.md and the voice rules in `.claude/rules/docs-voice.md`.
