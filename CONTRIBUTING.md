# Contributing

## PR Title Convention

This project uses [Conventional Commits](https://www.conventionalcommits.org/) for **PR titles**. Since we squash-merge, the PR title becomes the final commit message.

### Format

```
type: description
```

### Allowed Types

| Type | Purpose |
|------|---------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation only |
| `style` | Code style (formatting, semicolons, etc.) |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or updating tests |
| `build` | Build system or external dependencies |
| `ci` | CI configuration |
| `chore` | Other changes that don't modify src or test files |
| `revert` | Reverts a previous commit |

### Examples

- `feat: add user authentication`
- `fix: handle empty input`
- `docs: update installation instructions`

### Individual Commits

Individual commit messages within a PR are free-form. Only the PR title is enforced.
