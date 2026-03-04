Review and update README.md for this repository.

## Steps

1. Read `README.md` in full.

2. **Link audit** — Extract every `[text](path)` link that points to a local file. Verify each target exists using Glob. Report any broken links.

3. **Configuration Reference table** — Compare the table rows against actual files in the repo. Flag missing entries, stale entries, or wrong paths.

4. **Persona review** — Read through the README from each of these adopter perspectives and identify gaps:

   - **Python dev, knows CI basics** — Does the setup walkthrough (steps 1–7) answer their questions? Are the instructions complete and in the right order?
   - **Experienced CI user** — Is the two-tag flow clearly explained? Are the composite actions documented well enough to use in their own workflows?
   - **Existing user, customizing** — Are the action inputs (`body`, `commit-parsers`, etc.) documented? Can they figure out how to override defaults?

5. **Pitfalls review** — Check each pitfall for relevance. Flag any that are outdated or no longer applicable. Note any new pitfalls discovered during the review.

6. **Report** — Summarize findings before making changes: broken links, stale references, persona gaps, pitfall status.

7. **Apply fixes** — Update README.md to address all findings. Follow the README rules in CLAUDE.md.
