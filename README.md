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

A reference implementation for automated changelog and release workflows
using Conventional Commits.

## 📖 Features

- PR title prefixes (e.g., `feat:`, `fix:`) validated against
  [Conventional Commits]
- Breaking changes marked with `!` (e.g., `feat!:`)
- PR labels assigned automatically based on the prefix
- GitHub Releases published automatically after a `u`-prefixed version tag
  is pushed
- Release notes and `CHANGELOG.md` generated from PR titles
- A rolling `latest` tag that always points at the newest release

[Conventional Commits]: https://www.conventionalcommits.org/

## Set up in your repository

Setting up assumes a GitHub repository with Actions enabled; only the
version-bump step is hatch-specific — any tool that commits a version
bump and pushes a `u<version>` tag (e.g., `u1.2.3`) can drive the rest of
the pipeline.

### 🔧 Copy the pipeline files

1. User copies the five workflow files in [`.github/workflows/`] and the
   release-notes configuration [`.github/release.yml`].
2. User rewrites the import smoke test in `ci.yml` — the module name and
   the `legendary_octo_happiness.hello()` demo call — for their own
   package, and sets `--python` to their `requires-python` floor. The
   mypy target `src` needs no change for a src-layout package.

[`.github/workflows/`]: .github/workflows/
[`.github/release.yml`]: .github/release.yml

### 🔧 Configure version bumps

Stock hatch only rewrites the version number; the [hatch-regex-commit]
plugin makes `hatch version` also create the bump commit and the `u` tag
that triggers the pipeline. User installs [hatch] locally and adds to
`pyproject.toml`:

```toml
[build-system]
requires = ["hatchling", "hatch-regex-commit"]
build-backend = "hatchling.build"

[tool.hatch.version]
source = "regex_commit"
path = "src/your_package/__about__.py"
tag_name = "u{new_version}"
tag_sign = false
check_dirty = false
```

User replaces `your_package` with the package's import name and creates
the version file with an initial version (e.g., `__version__ = "0.1.0"`);
`[project]` must list `version` under `dynamic`, as [`pyproject.toml`] in
this repository does.

[hatch]: https://hatch.pypa.io/
[hatch-regex-commit]: https://pypi.org/project/hatch-regex-commit/
[`pyproject.toml`]: pyproject.toml

### 🔧 Configure the repository

1. User sets the merge methods under `Settings` > `General` >
   `Pull Requests`:
   - **Disable** "Allow merge commits"
   - **Enable** "Allow squash merging", with "Default commit message" set
     to "Pull request title and description"
   - **Disable** "Allow rebase merging"
2. User documents the PR title convention for contributors (the allowed
   type prefixes and the `!` breaking-change marker); this repository's
   [CONTRIBUTING.md] serves as a template.
3. User creates no labels: a label missing when the labeler first applies
   it is created automatically, with a gray default color. The twelve
   labels — `breaking`, `feature`, `fix`, `documentation`, `style`,
   `refactor`, `performance`, `test`, `build`, `ci`, `chore`, and
   `revert` — match the categories in [`.github/release.yml`]; that file
   and the labeler workflow [`conventional-label.yml`] must stay
   consistent if either changes.
4. Optional: User requires the "Conventional Commits" status check through
   a branch ruleset — by default it reports a status that nothing
   enforces. Any rule must still allow the pipeline's direct pushes to
   `main` (the changelog commit), or releases will fail.

[CONTRIBUTING.md]: CONTRIBUTING.md

## Release process

How a release is cut, and the design behind it.

### 🔧 Cut a release

1. User bumps the version: `hatch version <rule>`, where `<rule>` is
   `patch`, `minor`, or `major` (e.g., `hatch version minor`). This
   updates `src/legendary_octo_happiness/__about__.py`, creates the bump
   commit, and tags it `u<version>` (e.g., `u1.2.3`).
2. User pushes the commit and the tag created in step 1:
   `git push origin main u1.2.3`.
3. GitHub Actions ("Generate changelog") commits the updated
   `CHANGELOG.md` to `main` and tags that commit `v<version>` (e.g.,
   `v1.2.3`).
4. GitHub Actions ("Release a new version") creates a GitHub Release from
   the `v` tag and moves the `latest` tag to it.
5. User confirms the new version on the [Releases page][release-url]; the
   release badge above updates as well.
6. User pulls the changelog commit and the new tags:
   `git pull --tags --force origin main` (`--force` lets the moved
   `latest` tag update; without it the fetch is rejected).

The two automated steps appear as workflow runs on the Actions tab; the
Changelog and Release badges above link to each workflow's runs.

If the "Generate changelog" run fails, a "Release a new version" run
still appears, but its job is skipped and no release is created. The
common signature, "Tag u1.2.3 is not on main" in the log, means the bump
commit did not reach `main` — git pushes the branch and the tag
independently, so step 2 can land the tag while the `main` push is
rejected. The failure happens before the pipeline pushes anything, so
recovery is safe: User pushes the bump commit (`git push origin main`),
then re-triggers the pipeline by deleting the trigger tag on GitHub
(`git push origin --delete u1.2.3`) and pushing it again
(`git push origin u1.2.3`).

### 📖 Why the pipeline is built this way

- **One prefix, three outputs.** Squash-only merging lands every PR as a
  single commit whose message is the PR title, a validated Conventional
  Commits line. That one line becomes three outputs: the commit message on
  `main`, the `CHANGELOG.md` section [git-cliff] builds from it, and the
  release-notes category GitHub resolves through the label assigned from
  the same prefix (categories in [`.github/release.yml`]).
- **Two tags, one trigger.** Pushing the `u` tag is the only manual step
  that triggers the pipeline. The changelog commit and the `v` tag are
  pushed with the workflow token, and GitHub starts no workflows for
  pushes made with that token — a guard against recursive workflows. The
  Release workflow therefore chains on the "Generate changelog" run
  completing (`workflow_run`) rather than on the `v` tag, and its job
  runs only if that run succeeded.
- **One release at a time.** The pipeline assumes one release in progress
  at a time; the next `u` tag is pushed only after the previous release
  has appeared.
- **Releases are cut on `main`.** The changelog step fails unless the
  `u`-tagged commit is on `main`, and the changelog commit — and with it
  the `v` tag and the release — is created on `main`'s tip at run time: a
  PR squash-merged after the `u` tag is pushed is included in the release
  and its changelog. The step pushes directly to `main`, so
  branch-protection rules that block direct pushes would break it.
- **Permanent and rolling tags.** `v` tags are permanent; `latest` moves
  to every new release commit so links and install instructions can
  reference one stable name.

[git-cliff]: https://git-cliff.org/

## 📋 GitHub Actions workflows

The following workflows run on GitHub Actions:

| Workflow                   | Trigger                              | Purpose                                                            |
| -------------------------- | ------------------------------------ | ------------------------------------------------------------------ |
| [`ci.yml`]                 | PR, push to `main`                   | Build sdist and wheel, import from the wheel, type-check with mypy |
| [`pr-title.yml`]           | PR `opened`/`edited`/`synchronize`   | Validate the PR title against Conventional Commits                 |
| [`conventional-label.yml`] | PR `opened`/`edited`                 | Label the PR from its title prefix                                 |
| [`changelog.yml`]          | `u*.*.*` tag pushed (e.g., `u1.2.3`) | Generate `CHANGELOG.md`, create the `v` tag                        |
| [`release.yml`]            | "Generate changelog" completed       | Create the GitHub Release, move `latest`                           |

[`ci.yml`]: .github/workflows/ci.yml
[`pr-title.yml`]: .github/workflows/pr-title.yml
[`conventional-label.yml`]: .github/workflows/conventional-label.yml
[`changelog.yml`]: .github/workflows/changelog.yml
[`release.yml`]: .github/workflows/release.yml

The changelog trigger requires the full three-part pattern: a `u1.2` tag
starts nothing. The two PR-triggered workflows run on
`pull_request_target`, so labeling works on PRs from forks; neither checks
out the PR's code. The changelog and release workflows run with
`contents: write`; the labeler runs with `pull-requests: write`. The
workflows assume squash merging is the only enabled merge method, with
"Default commit message" set to "Pull request title and description".

## 📋 Pinned actions

Every action the workflows use is pinned to a commit SHA, with a comment
recording the corresponding release version. [TaiSakuma/changelog-commit]
and [TaiSakuma/checkout-version-tag] are composite actions developed for
this project.

| Action                                 | Used by                  | Purpose                                                    |
| -------------------------------------- | ------------------------ | ---------------------------------------------------------- |
| [TaiSakuma/changelog-commit]           | `changelog.yml`          | Generate the changelog, commit it, create the release tag  |
| [TaiSakuma/checkout-version-tag]       | `release.yml`            | Derive and check out the release tag from the trigger tag  |
| [amannn/action-semantic-pull-request]  | `pr-title.yml`           | Validate the PR title                                      |
| [bcoe/conventional-release-labels]     | `conventional-label.yml` | Label the PR from its title prefix                         |
| [EndBug/latest-tag]                    | `release.yml`            | Move the `latest` tag                                      |
| [actions/checkout]                     | `ci.yml`                 | Check out the repository                                   |
| [astral-sh/setup-uv]                   | `ci.yml`                 | Install uv                                                 |

[TaiSakuma/changelog-commit]: https://github.com/marketplace/actions/changelog-commit
[TaiSakuma/checkout-version-tag]: https://github.com/marketplace/actions/checkout-version-tag
[amannn/action-semantic-pull-request]: https://github.com/amannn/action-semantic-pull-request
[bcoe/conventional-release-labels]: https://github.com/bcoe/conventional-release-labels
[EndBug/latest-tag]: https://github.com/EndBug/latest-tag
[actions/checkout]: https://github.com/actions/checkout
[astral-sh/setup-uv]: https://github.com/astral-sh/setup-uv
