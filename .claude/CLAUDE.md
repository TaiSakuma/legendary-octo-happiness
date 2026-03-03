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

## Releases

Releases use a two-tag flow:

1. Bump version with `hatch version <rule>`, commit, tag `u<version>`, push.
2. The Changelog workflow (triggered by the `u` tag) generates `CHANGELOG.md`, commits it, creates the `v` tag, and pushes.
3. The Release workflow (triggered via `workflow_run` after Changelog) creates a GitHub Release with GitHub auto-generated notes.
