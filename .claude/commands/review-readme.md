Review and update README.md for this repository.

## Steps

1. Read `README.md` in full.

2. **Link audit** — Extract every `[text](path)` link that points to a local file. Verify each target exists using Glob. Report any broken links.

3. **Workflows table** — Compare each row of the "GitHub Actions workflows" table against the actual files in `.github/workflows/`. Verify the file exists, the trigger column matches the workflow's `on:` block, and the purpose column matches what the workflow does. Flag workflows that exist but are missing from the table.

4. **Release process** — Verify the numbered steps in the "Release process" section match the actual behavior of `changelog.yml` and `release.yml`: the trigger tag pattern, the generated `v` tag, and the GitHub Release creation.

5. **GitHub settings** — Check that the repository settings listed in the README are still what the workflows assume (squash merge only, with "Pull request title and description" as the default commit message).

6. **Marketplace actions** — Verify the "Marketplace actions" section lists the composite actions the workflows actually use and that the marketplace links resolve.

7. **Persona review** — Read the README as someone copying this reference setup into their own repository: can they understand the two-tag flow, add the workflows, and configure the repository settings from the README alone? Identify gaps.

8. **Report** — Summarize findings before making changes: broken links, stale table rows, mismatches with workflow files, persona gaps.

9. **Apply fixes** — Update README.md to address all findings. Follow the README rules in CLAUDE.md.
