# legendary-octo-happiness

A sandbox repository for testing pull request workflows, releases, and changelog automation. The configurations here serve as a reference to apply to other repositories.

## What's configured

### PR title convention

PR titles must follow [Conventional Commits](https://www.conventionalcommits.org/) format (e.g., `feat: add feature`). This is enforced by a CI check using [`amannn/action-semantic-pull-request`](https://github.com/amannn/action-semantic-pull-request). See [CONTRIBUTING.md](CONTRIBUTING.md) for allowed types.

### Squash merge only

The repository is configured to only allow **squash merges**. The PR title becomes the squash commit message, ensuring every commit on `main` follows Conventional Commits. Head branches are automatically deleted after merge.

### Automated releases

Pushing a tag matching `v*.*.*` triggers a GitHub Actions workflow that creates a GitHub Release with auto-generated release notes.

### Categorized release notes

PRs are auto-labeled based on their Conventional Commits title prefix (e.g., `feat:` → `feature` label) using [`bcoe/conventional-release-labels`](https://github.com/bcoe/conventional-release-labels). Release notes are then grouped into categories (Features, Bug Fixes, etc.) via `.github/release.yml`. Dependabot PRs are excluded from release notes.

### PR template

A pull request template (`.github/pull_request_template.md`) reminds contributors to use the Conventional Commits format.
