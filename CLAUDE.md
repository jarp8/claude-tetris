# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No dependencies, no build, no tests, no linter, no `package.json`. README is in Spanish.

## Run

Open `index.html` directly, or serve statically: `python -m http.server 8000` → `http://localhost:8000`.

## Architecture

Three files: `index.html` (DOM + two canvases), `style.css` (dark/retro theme), `game.js` (all logic, ~300 lines, single `'use strict'` script, top-level globals, no modules).

- `game.js` caches DOM nodes by id at top (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`). Renaming ids in `index.html` breaks it.
- Board = `ROWS × COLS` matrix; cell is `0` or color/piece index 1–7. `COLORS` and `PIECES` are indexed by that same number (index 0 = `null`), so piece shapes embed their own index.
- Canvas size is coupled to constants: `#board` `width/height` in `index.html` must equal `COLS×BLOCK` × `ROWS×BLOCK` (300×600). Change both together.
- Flow: `init()` → `spawn()` → `requestAnimationFrame(loop)`. `loop` accumulates dt vs `dropInterval`, drops piece or `lockPiece()` (merge → `clearLines` → score/level → `spawn`). `spawn` colliding immediately → `endGame()`.
- Rotation: `rotateCW` (transpose + reverse), `tryRotate` wall-kicks by ±1/±2 columns only.
- Scoring/speed: `LINE_SCORES` × level; hard drop +2/cell, soft drop +1/row; level +1 per 10 lines; `dropInterval = max(100, 1000 − (level−1)×90)`.
- Restart button calls `init()`; all game state lives in the shared `let` globals reset by `init()`.

## Issue triage

`.github/workflows/claude-issue-triage.yml`: on issue opened / title-or-body edited, Claude assigns labels (type + `area:*` + `priority:*`, existing labels only) and posts/updates a single diagnosis comment (marker `<!-- claude-triage -->`). Read-only tools + `gh issue`; no code changes. Uses secret `CLAUDE_CODE_OAUTH_TOKEN`.
