# legendary-octo-happiness

[![GitHub Release][release-badge]][release-url]
[![CI][ci-badge]][ci-url]
[![Changelog][changelog-badge]][changelog-url]
[![Release][release-wf-badge]][release-wf-url]
[![License: MIT][license-badge]][license-url]

[release-badge]: https://img.shields.io/github/v/release/TaiSakuma/legendary-octo-happiness
[release-url]: https://github.com/TaiSakuma/legendary-octo-happiness/releases/latest
[ci-badge]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/ci.yml/badge.svg
[ci-url]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/ci.yml
[changelog-badge]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/changelog.yml/badge.svg
[changelog-url]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/changelog.yml
[release-wf-badge]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/release.yml/badge.svg
[release-wf-url]: https://github.com/TaiSakuma/legendary-octo-happiness/actions/workflows/release.yml
[license-badge]: https://img.shields.io/badge/License-MIT-blue.svg
[license-url]: LICENSE

A reference implementation for automated changelog and release workflows using
Conventional Commits.

## 📖 Features

- PR title prefixes (e.g., `feat:`, `fix:`) enforced with [Conventional Commits]
- Breaking changes marked with `!` (e.g., `feat!:`)
- PR labels assigned automatically based on the prefix
- GitHub Releases published automatically after a `u`-prefixed version tag
  is pushed
- Release notes and `CHANGELOG.md` generated from PR titles

[Conventional Commits]: https://www.conventionalcommits.org/

## Release process

1. User tags a version with `u`-prefix (e.g., `u1.2.3`) to the main branch.\*
2. User pushes the commit and the tag to GitHub
   (`git push origin main --tags`).
3. GitHub Actions adds a new commit with `CHANGELOG.md` update.
4. GitHub Actions tags the commit with a `v`-prefixed version (e.g., `v1.2.3`).
5. GitHub Actions makes a GitHub Release with release notes.
6. GitHub Actions moves the `latest` tag to the new release.

\* _In this repo, `hatch version <rule>` (e.g., `hatch version minor`) bumps
the version, creates the commit, and creates the `u` tag._

## 📋 GitHub Actions workflows

The following workflows run on GitHub Actions:

| Workflow                   | Trigger                        | Purpose                                  |
| -------------------------- | ------------------------------ | ---------------------------------------- |
| [`ci.yml`]                 | PR, push to `main`             | Build and type-check the package         |
| [`pr-title.yml`]           | PR opened/edited/synced        | Validate PR title                        |
| [`conventional-label.yml`] | PR opened/edited               | Label PR by convention                   |
| [`changelog.yml`]          | `u*.*.*` tag pushed            | Generate `CHANGELOG.md`, `v` tag         |
| [`release.yml`]            | "Generate changelog" completed | Create GitHub Release, move `latest` tag |

[`ci.yml`]: .github/workflows/ci.yml
[`pr-title.yml`]: .github/workflows/pr-title.yml
[`conventional-label.yml`]: .github/workflows/conventional-label.yml
[`changelog.yml`]: .github/workflows/changelog.yml
[`release.yml`]: .github/workflows/release.yml

These workflows require the GitHub settings:

- `Settings` > `General` > `Pull Requests`
  - **Disable** "Allow merge commits"
  - **Enable** "Allow squash merging"
    - Default commit message: "Pull request title and description"
  - **Disable** "Allow rebase merging"

## 📋 Marketplace actions

The workflows use two composite actions developed for this project:

- [TaiSakuma/changelog-commit] — Generate changelog, commit, and create release tag
- [TaiSakuma/checkout-version-tag] — Derive and check out a release tag from a trigger tag

[TaiSakuma/changelog-commit]: https://github.com/marketplace/actions/changelog-commit
[TaiSakuma/checkout-version-tag]: https://github.com/marketplace/actions/checkout-version-tag
