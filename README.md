# legendary-octo-happiness

[![GitHub Release][release-badge]][release-url]
[![Changelog][changelog-badge]][changelog-url]
[![Release][release-wf-badge]][release-wf-url]
[![License: MIT][license-badge]][license-url]

[release-badge]: https://img.shields.io/github/v/release/TaiSakuma/legendary-octo-happiness
[release-url]: https://github.com/TaiSakuma/legendary-octo-happiness/releases/latest
[changelog-badge]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/changelog.yml/badge.svg
[changelog-url]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/changelog.yml
[release-wf-badge]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/release.yml/badge.svg
[release-wf-url]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/release.yml
[license-badge]: https://img.shields.io/badge/License-MIT-blue.svg
[license-url]: LICENSE

A reference implementation for automated changelog and release workflows using
Conventional Commits.

## Features

- PR title prefixes (e.g., `feat:`, `fix:`) enforced with [Conventional Commits]
- Breaking changes marked with `!` (e.g., `feat!:`)
- PR labels assigned automatically based on the prefix
- GitHub Releases triggered by version tags
- Release notes and `CHANGELOG.md` generated from PR titles

[Conventional Commits]: https://www.conventionalcommits.org/

## Release process

1. User tags a version with `u`-prefix (e.g., `u1.2.3`) to the main branch.\*
2. User pushes the tag to GitHub.
3. GitHub Actions adds a new commit with `CHANGELOG.md` update.
4. GitHub Actions tags the commit with a `v`-prefixed version (e.g., `v1.2.3`).
5. GitHub Actions makes a GitHub Release with release notes.

\* _In this repo, `hatch version <rule>` bumps the version and creates the `u`
tag._

## GitHub Actions workflows

The following workflows run on GitHub Actions:

| Workflow                   | Trigger                      | Purpose                          |
| -------------------------- | ---------------------------- | -------------------------------- |
| [`pr-title.yml`]           | PR opened/edited             | Validate PR title                |
| [`conventional-label.yml`] | PR opened/edited             | Label PR by convention           |
| [`changelog.yml`]          | `u*` tag pushed              | Generate `CHANGELOG.md`, `v` tag |
| [`release.yml`]            | Changelog workflow completed | Create GitHub Release            |

[`pr-title.yml`]: .github/workflows/pr-title.yml
[`conventional-label.yml`]: .github/workflows/conventional-label.yml
[`changelog.yml`]: .github/workflows/changelog.yml
[`release.yml`]: .github/workflows/release.yml

These workflows require the GitHub settings:

- `Settings` > `General` > `Pull Requests`
  - **Disable** "Allow merge commits"
  - **Enable** "Allow squash merges"
    - Default commit message: "Pull request title and description"
  - **Disable** "Allow rebase merges"

## Marketplace actions

The workflows use two composite actions developed for this project:

- [TaiSakuma/changelog-commit] — Generate changelog, commit, and create release tag
- [TaiSakuma/checkout-version-tag] — Derive and check out a release tag from a trigger tag

[TaiSakuma/changelog-commit]: https://github.com/marketplace/actions/changelog-commit
[TaiSakuma/checkout-version-tag]: https://github.com/marketplace/actions/checkout-version-tag
