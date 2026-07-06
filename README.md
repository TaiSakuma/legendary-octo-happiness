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
- Releases cut from any chosen commit, not only `main`'s head
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
   `main` (the merge-back that lands eligible releases on `main`), or
   releases will fail; a rule requiring linear history would also block
   the merge-commit case (a release cut from an older commit already on
   `main`).

[CONTRIBUTING.md]: CONTRIBUTING.md

## Release process

How a release is cut, and the design behind it.

### 🔧 Cut a release

1. User checks out the commit to release: `git switch main && git pull`
   for the newest state, or `git switch --detach <commit>` (e.g.,
   `git switch --detach 1a2b3c4`) for an older one. The chosen commit
   must already contain the pipeline's workflow files — a tag push runs
   the workflow files as of the tagged commit.
2. User bumps the version: `hatch version <rule>`, where `<rule>` is
   `patch`, `minor`, or `major` (e.g., `hatch version minor`). This
   updates `src/legendary_octo_happiness/__about__.py`, creates the bump
   commit, and tags it `u<version>` (e.g., `u1.2.3`).
3. User pushes only the tag, reading its name from hatch's output or
   `git tag`: `git push origin u1.2.3`. (From `main`, pushing the branch
   and the tag together — `git push origin main u1.2.3` — behaves the
   same.)
4. GitHub Actions ("Generate changelog") creates the branch
   `release/<version>` (e.g., `release/1.2.3`) at the tagged commit,
   commits the updated `CHANGELOG.md` on it, and tags that commit
   `v<version>` (e.g., `v1.2.3`).
5. GitHub Actions ("Generate changelog") merges the release branch into
   `main` and deletes it — a fast-forward when cut from `main`'s head, a
   merge commit otherwise — provided the chosen commit is on `main` and
   the newest existing release by version order is already part of its
   history; otherwise the release is a backport: GitHub Actions flags the
   run with a warning, the branch remains as the maintenance line, and
   `main` is untouched.
6. GitHub Actions ("Release a new version") creates a GitHub Release from
   the `v` tag; when the new version is the newest by version order, the
   release is marked as the latest release and the `latest` tag moves to
   it.
7. User confirms the new version on the [Releases page][releases]; when
   the release is marked as the latest release, the GitHub Release badge
   above updates as well.
8. User checks the repository's branches: a `release/<version>` branch
   remains until its release merges into `main`; once merged, it is
   deleted. After a merge-back, User returns to `main` if needed
   (`git switch main`) and pulls the changelog commit and the new tags:
   `git pull --tags --force origin main` (`--force` lets the moved
   `latest` tag update; without it the fetch is rejected). Otherwise,
   `main` has nothing new; `git fetch --tags --force origin` retrieves
   the release branch and the tags.

The automated steps appear as workflow runs on the Actions tab; the
Changelog and Release badges above link to each workflow's runs.

If the "Generate changelog" run fails, a "Release a new version" run
still appears, but its job is skipped and no release is created. User
deletes the trigger tag on GitHub (`git push origin --delete u1.2.3`),
deletes the `release/1.2.3` branch if the failed run left one behind
(`git push origin --delete release/1.2.3`), and, if the failed run had
already created the `v` tag (visible under the repository's tags),
deletes that tag too (`git push origin --delete v1.2.3`); then User fixes
the cause and pushes the trigger tag again (`git push origin u1.2.3`).

If the chosen commit predates the pipeline (the step 1 prerequisite),
pushing the tag starts no workflow run at all — nothing appears on the
Actions tab. User confirms the commit carries the workflow files, then
bumps and tags again from a commit that does.

[releases]: https://github.com/TaiSakuma/legendary-octo-happiness/releases

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
- **Every release gets its own branch.** When the `u` tag is pushed,
  GitHub Actions creates `release/<version>` at the tagged commit and
  builds the release there: the changelog commit and the `v` tag sit
  directly on top of the tagged commit. A release therefore contains
  exactly the commit that was tagged plus the changelog commit — a PR
  merged after the tag push is not included; it reaches the release line
  through `main` and the next release.
- **`main` follows through the merge-back.** GitHub Actions merges the
  release branch into `main` and deletes it, provided the chosen commit
  is on `main` and the newest existing release by version order is
  already part of its history — a fast-forward when the release was cut
  from `main`'s head, a merge commit otherwise. The merge-back pushes
  directly to `main`, so branch-protection rules that block direct
  pushes would break it.
- **Backports.** When the merge-back precondition fails — the chosen
  commit is not on `main`, or the newest existing release by version
  order is not part of its history — the merge-back is skipped and
  GitHub Actions flags the run with a warning: `release/<version>`
  remains as the maintenance line, and `main` (including its
  `CHANGELOG.md`) is untouched. The next release on that
  line is cut from the branch's `v`-tagged commit by the same procedure;
  its changelog lives only on that branch — `main`'s `CHANGELOG.md`
  never lists backports.
- **Permanent and rolling tags.** `v` tags are permanent; the GitHub
  Release is marked as the latest release and the `latest` tag moves only
  when the new version is the newest by version order.

[git-cliff]: https://git-cliff.org/

### 📖 How a release moves through git

Cutting a release from `main`'s own current head `C` — the simplest
case:

```text
---o---o---C                            <- main
            \
             u1.2.3---v1.2.3            <- release/1.2.3
```

```text
---o---o---C---u1.2.3---v1.2.3          <- main
```

Since `C` is already `main`'s head, the merge-back is a fast-forward:
`main` simply extends through the branch's two commits, and
`release/1.2.3` is deleted with no merge commit.

Cutting from an old commit `C` — one that already has commits after it
on `main` — needs a real merge instead:

```text
---o---o---C---o---o                    <- main
            \
             u1.3.0---v1.3.0            <- release/1.3.0
```

```text
---o---o---C---o---o-------M            <- main
            \             /
             u1.3.0---v1.3.0            <- release/1.3.0 (deleted after merge)
```

A backport looks the same up to the branch, but the merge-back does not
happen:

```text
---o---C---o---u1.3.0---v1.3.0---o     <- main
        \
         u1.2.4---v1.2.4               <- release/1.2.4 (kept, unmerged)
```

1.2.4 is cut from `C`, but 1.3.0 already landed after `C` — the newest
release (`v1.3.0`) is not an ancestor of `C`, so the merge-back is
skipped. `release/1.2.4` stays as the 1.2.x maintenance line.

The next 1.2.x release branches off `v1.2.4`'s commit as its own new
branch, not a continuation of the old one:

```text
---o---C---o---u1.3.0---v1.3.0---o     <- main
        \
         u1.2.4---v1.2.4               <- release/1.2.4 (kept)
                       |
                       +---u1.2.5---v1.2.5   <- release/1.2.5 (new branch)
```

`release/1.2.5` forks from `release/1.2.4`'s tip; `release/1.2.4` itself
is untouched. `release/1.2.5` is cut from a commit that is not on `main`
either, so it is itself a backport — its own merge-back is skipped by
the same rule, for the same reason.

Two releases cut from the same commit `C` around the same time (`1.3.0`
a minor bump, `2.0.0` a major bump — both reachable from the same
starting version, unlike two minor bumps would be):

```text
             +---u1.3.0---v1.3.0     <- release/1.3.0
            /
---o---o---C                          <- main
            \
             +---u2.0.0---v2.0.0     <- release/2.0.0
```

Both branches are created and both build validly. Whichever merge-back
completes first — say 1.3.0 — advances `main` past `C`:

```text
---o---o---C---u1.3.0---v1.3.0        <- main (1.3.0 merged first)
            \
             +---u2.0.0---v2.0.0     <- release/2.0.0 (unmerged)
```

2.0.0's precondition is now checked against this new state: is the
newest release (`v1.3.0`, which just landed) an ancestor of `C` — that
is, already part of `C`'s history? No — so 2.0.0's merge-back is skipped
and flagged with a warning, exactly like a backport, even though it was
an ordinary release. This is why the pipeline assumes one release in
progress at a time: releasing 2.0.0 only after 1.3.0 has appeared means
its checkout starts from the already-advanced `main`, not the stale `C`.
2.0.0 is still the newest release by version order, so it is marked
latest regardless — leaving `latest` on a commit `main` never absorbs,
until someone resolves the situation manually.

## 📋 GitHub Actions workflows

The following workflows run on GitHub Actions:

| Workflow                   | Trigger                              | Purpose                                                            |
| -------------------------- | ------------------------------------ | ------------------------------------------------------------------ |
| [`ci.yml`]                 | PR, push to `main`                   | Build sdist and wheel, import from the wheel, type-check with mypy |
| [`pr-title.yml`]           | PR `opened`/`edited`/`synchronize`   | Validate the PR title against Conventional Commits                 |
| [`conventional-label.yml`] | PR `opened`/`edited`                 | Label the PR from its title prefix                                 |
| [`changelog.yml`]          | `u*.*.*` tag pushed (e.g., `u1.2.3`) | Generate `CHANGELOG.md`, create the `v` tag, merge back to `main`  |
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
