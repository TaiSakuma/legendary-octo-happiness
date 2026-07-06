# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Python package `legendary-octo-happiness` using Hatchling as the build backend. Requires Python >=3.12. Source lives in `src/legendary_octo_happiness/`. The package includes a `py.typed` marker (PEP 561).

## Build & Install

```bash
uv pip install -e .        # editable install
uv build                   # build sdist/wheel
```

## PR Titles

PR titles must follow [Conventional Commits](https://www.conventionalcommits.org/) (e.g., `feat: add feature`). See [CONTRIBUTING.md](../CONTRIBUTING.md) for allowed types.

## README

Use `/review-readme` to audit and update, and `/write-readme` to author or substantially revise the README. Key rules: links must resolve, the workflows table must match the actual workflow files and their triggers, each section keeps to its declared Diátaxis quadrant (`.claude/rules/diataxis-review.md`), and prose follows `.claude/rules/docs-voice.md`.

## Releases

Releases use a two-tag flow. A release can be cut from any commit that already carries the workflow files, not only `main`'s head:

1. Check out the commit to release, bump the version with `hatch version <rule>` (this commits and creates the annotated `u<version>` tag), and push only that tag (`git push origin u<version>`).
2. The Changelog workflow (triggered by the `u` tag) creates a `release/<version>` branch at the tagged commit, generates `CHANGELOG.md` and the `v` tag on it, then merges the branch back into `main` when the release is on `main`'s line and its history already contains the newest existing release. Otherwise it is a backport: the merge-back is skipped with a warning, the branch is kept, and `main` is untouched.
3. The Release workflow (triggered via `workflow_run` after Changelog) creates a GitHub Release with GitHub auto-generated notes, marking it latest and moving the `latest` tag only when it is the newest version.
