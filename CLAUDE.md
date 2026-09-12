# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A classic Tetris implementation in vanilla JavaScript, HTML5 Canvas, and CSS. No dependencies, no build process, no package.json.

## Running the game

Open `index.html` directly in a browser, or serve it statically:

```bash
python3 -m http.server 8000
# or
npx serve .
```

There are no build, lint, or test commands — the game is three static files (`index.html`, `style.css`, `game.js`) loaded as-is.

## Architecture

Everything lives in `game.js` (~300 lines) as a single flat script with module-level state (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, etc.) — there are no classes or modules.

- **Board model**: `board` is a `ROWS × COLS` matrix where each cell is `0` (empty) or a piece color index (1–7).
- **Pieces**: `PIECES` defines the 7 tetrominoes as square matrices. Rotation (`rotateCW`) transposes + reverses rows; `tryRotate` applies wall kicks (tries offsets `[0, -1, 1, -2, 2]`) before discarding a rotation that collides.
- **Collision** (`collide`): checks board bounds and existing locked cells.
- **Game loop** (`loop`): driven by `requestAnimationFrame`, accumulates elapsed time (`dropAccum`) and drops the current piece one row when `dropInterval` is exceeded.
- **Locking pieces** (`lockPiece` → `merge` + `clearLines` + `spawn`): fixes the piece into `board`, clears full rows (scanning bottom-up, splicing/unshifting), then spawns the next piece.
- **Scoring**: `LINE_SCORES = [0, 100, 300, 500, 800]` multiplied by `level`; hard drop adds 2 points/row, soft drop adds 1 point/row.
- **Leveling**: level increases every 10 lines; `dropInterval = max(100, 1000 - (level - 1) * 90)` ms.
- **Ghost piece** (`ghostY`): projects the current piece straight down to its landing row, drawn at `globalAlpha = 0.2`.
- **Rendering**: `draw()` redraws the full board canvas each frame (grid, locked blocks, ghost, current piece); `drawNext()` renders the next-piece preview on a separate canvas.
- **Input**: a single `keydown` listener handles movement/rotation/soft-drop/hard-drop/pause; `P` toggles pause independent of game-over state.
- **Game over**: triggered in `spawn()` when a newly spawned piece immediately collides; shows the overlay via `endGame()`. The same overlay element is reused for both PAUSA and GAME OVER, distinguished by `overlayTitle`/`overlayScore` text.

## Tunable constants (in `game.js`)

`COLS`, `ROWS`, `BLOCK`, `COLORS`, `LINE_SCORES`, `dropInterval`. If `COLS`, `ROWS`, or `BLOCK` change, update the `<canvas id="board">` `width`/`height` in `index.html` to match (`COLS × BLOCK` and `ROWS × BLOCK`).
