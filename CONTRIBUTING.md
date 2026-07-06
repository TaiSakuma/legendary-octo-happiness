# Contributing

## PR Title Convention

This project uses [Conventional Commits](https://www.conventionalcommits.org/) for **PR titles**. Since we squash-merge, the PR title becomes the final commit message.

### Format

```text
type: description
```

### Allowed Types

| Type       | Purpose                                                 |
| ---------- | ------------------------------------------------------- |
| `feat`     | A new feature                                           |
| `fix`      | A bug fix                                               |
| `docs`     | Documentation only                                      |
| `style`    | Code style (formatting, semicolons, etc.)               |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf`     | Performance improvement                                 |
| `test`     | Adding or updating tests                                |
| `build`    | Build system or external dependencies                   |
| `ci`       | CI configuration                                        |
| `chore`    | Other changes that don't modify src or test files       |
| `revert`   | Reverts a previous commit                               |

Append `!` to indicate breaking changes (e.g., `feat!: description`).

### Examples

- `feat: add user authentication`
- `fix: handle empty input`
- `docs: update installation instructions`
- `feat!: remove get_user()`

### Individual Commits

Individual commit messages within a PR are free-form. Only the PR title is enforced.

## Releasing

Releases use a two-tag flow. The `u` tag triggers changelog generation, which in turn creates the `v` tag and GitHub Release.

### Steps

1. **Bump the version:**

   ```bash
   hatch version <rule>
   ```

   Where `<rule>` is `patch`, `minor`, or `major`. This updates `src/legendary_octo_happiness/__about__.py`, creates a commit, and tags it `u<version>`.

2. **Push the tag:**

   ```bash
   git push origin u<version>
   ```

   Push only the `u` tag, not `main --tags`: a stale local `latest` tag makes `--tags` fail. GitHub Actions publishes the changelog commit and the other tags back to you.

3. **Wait for CI:**
   - The **Changelog** workflow creates a `release/<version>` branch at the tagged commit, generates `CHANGELOG.md` and the `v` tag on it, then merges the branch back into `main` (a backport, cut from a commit off `main`'s line, keeps the branch and leaves `main` untouched).
   - The **Release** workflow then creates a GitHub Release with categorized notes, marking it latest only when it is the newest version.

To release from an older commit rather than `main`'s head, check that commit out first (`git switch --detach <commit>`); see the README's "Release process" for the full runbook.
