# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Python package `legendary-octo-happiness` using Hatchling as the build backend. Requires Python >=3.12. Source lives in `src/legendary_octo_happiness/`. The package includes a `py.typed` marker (PEP 561).

## Build & Install

```bash
uv pip install -e .        # editable install
uv build                   # build sdist/wheel
```

## Releases

Releases are automated via GitHub Actions. Push a tag matching `v*.*.*` to trigger a GitHub Release with auto-generated notes.
