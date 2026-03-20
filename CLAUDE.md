# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a learning project containing browser-based games built as single-file HTML applications. No build tools, no dependencies, no server required — every file opens directly in a browser.

## Running the Games

Open any `.html` file directly in a browser (double-click or drag into browser window). There is no build step, no `npm install`, and no dev server.

## Architecture

Each game is a **single self-contained HTML file** with all CSS and JavaScript inlined:

- **`tictactoe.html`** — Standard 3×3 Tic Tac Toe with persistent score tracking (2-player, local).
- **`connection-game.html`** — A Blokus/Hex hybrid on a 20×20 grid. Blue connects Top↔Bottom, Red connects Left↔Right. Players place tetromino and pentomino pieces; groups are captured when an attacker covers both "tag ends" (cells beyond each endpoint of an opponent's connected group). Supports 2-player and Player vs Computer modes.

## Git Workflow

After completing any meaningful unit of work — a feature, a bug fix, a visual improvement — commit and push to GitHub immediately. Never leave work uncommitted at the end of a session.

- **Commit often**: one logical change per commit, not one giant commit per session
- **Push every commit**: `git push` after every commit so work is safe on GitHub
- **Commit messages**: short present-tense summary line, e.g. `Add capture animation with tag-end highlight` — no filler words, no "various changes"
- **Stage specific files**: never `git add .` blindly; name the files being committed
- **Check before finishing**: run `git status` at the end of every session to confirm nothing is left uncommitted

### Connection Game internals

- **Board** — `board` (20×20 `Uint8Array`, values 0/1/2) and `pieceBoard` (tracks which placement index occupies each cell, used for capture/return logic).
- **Pieces** — 19 shapes defined as `[row, col]` offset arrays in `DEFS`. `transform(id, rot, flip)` applies rotation/flip at runtime and normalises to top-left origin.
- **Capture rule** — `getTagEnds()` finds the cells directly beyond each degree-1 endpoint of a connected group. `checkCaptures()` scans for opponent groups where the attacker occupies every in-bounds tag end; captured pieces are returned to the attacker's hand.
- **Win detection** — `bfsEdge()` BFS from one edge to check connectivity.
- **AI** — `getAllMoves()` enumerates valid placements (deduplicating symmetric orientations). Three levels: Easy (random), Medium (spatial heuristic: column progress + adjacency + centre preference), Hard (top-40 medium candidates evaluated with `pathDist()` — an O(V²) Dijkstra giving the minimum empty cells needed to complete a winning path, applied to both players before/after simulating each move).
