# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A single-file, dependency-free Tic-Tac-Toe game (`tic-tac-toe.html`). There is no build step, package manager, test suite, or linter — the file is opened directly in a browser (e.g. double-click it, or `open tic-tac-toe.html`).

## Architecture

Everything (markup, CSS, and JS) lives in `tic-tac-toe.html`:

- **Styling** uses CSS custom properties on `:root` for theming, with light mode as the default and a dark palette applied both via `@media (prefers-color-scheme: dark)` and an explicit `:root[data-theme="dark"]` override, so the page supports OS-level dark mode as well as a manually forced theme.
- **Game logic** is a single IIFE at the bottom of the file. Board state is a flat 9-element array (`board`), win detection checks it against the 8 index triples in `WIN_LINES`, and all DOM updates (cell marks, status text, active-player score card, disabling cells) are driven imperatively from `playMove`, `newRound`, and `resetScores` — there is no virtual DOM or framework.
- **Score persistence** is handled by `loadScores`/`saveScores` via `localStorage` (key `ttt-scores`), wrapped in try/catch so the game still works if storage is unavailable (e.g. private browsing).
- **`window.claude.hot` integration**: the boot sequence at the end of the script checks for a `window.claude.hot` API (used when this file is run inside a Claude Artifact with hot-reload support). If present, it registers a `snapshot()` callback that persists `scores` across hot reloads and boots from `window.claude.hot.data` instead of `localStorage`; otherwise it falls back to the normal `loadScores()` path. Keep this fallback intact if editing the boot logic, since the file must also work as a plain static HTML file outside that environment.

## Version control

This project is tracked in git and pushed to GitHub (`anantkaushal02/tic-tac-toe`, public). Git/GitHub is the safety net for this project's history, so treat committing and pushing as part of doing the work, not an afterthought:

- Commit regularly as work progresses — after each meaningful, working change — rather than batching up a large, unrelated diff into one commit.
- Write clean, descriptive commit messages that explain *why* a change was made, not just what changed.
- Push to GitHub after committing so work is never left sitting only in the local repo. This is what lets us recover or revert to any prior state if something breaks.
