# legendary-octo-happiness

A sandbox repository for testing pull request workflows, releases, and changelog automation. The configurations here serve as a reference to apply to other repositories.

## What's configured

### PR title convention

PR titles must follow [Conventional Commits](https://www.conventionalcommits.org/) format (e.g., `feat: add feature`). This is enforced by a CI check using [`amannn/action-semantic-pull-request`](https://github.com/amannn/action-semantic-pull-request). See [CONTRIBUTING.md](CONTRIBUTING.md) for allowed types.

### Squash merge only

The repository is configured to only allow **squash merges**. The PR title becomes the squash commit message, ensuring every commit on `main` follows Conventional Commits. Head branches are automatically deleted after merge.

### Changelog

A `CHANGELOG.md` is generated automatically using [git-cliff](https://git-cliff.org/) and committed to the repository so it travels with forks and clones.

The release process uses a **two-tag approach**:

1. Bump the version with `hatch version <rule>` (e.g., `hatch version minor`), commit, tag with `u<version>` (e.g., `u1.1.0`), and push.
2. The **Changelog** workflow triggers on the `u` tag, generates `CHANGELOG.md` via git-cliff, commits it to `main`, creates the corresponding `v` tag (e.g., `v1.1.0`), and pushes.
3. The **Release** workflow triggers after the Changelog workflow completes, generates release notes via git-cliff, and creates a GitHub Release for the `v` tag.

This avoids force-tagging and ensures the `v` tag is always on the commit that includes the updated changelog. Tags created by `GITHUB_TOKEN` don't trigger `on: push` events, so the release workflow uses `workflow_run` chaining.

### Categorized release notes

Commits are categorized in both the changelog and release notes based on their Conventional Commits prefix (Features, Bug Fixes, Performance, Documentation, Refactoring, Testing, Build & CI, Reverted). The git-cliff configuration lives in `cliff.toml`.

### PR template

A pull request template (`.github/pull_request_template.md`) reminds contributors to use the Conventional Commits format.
