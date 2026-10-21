# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Classic Asteroids clone in plain HTML5 Canvas + vanilla JS (ES6+). No dependencies, no bundler, no build step, no tests, no linter. Live demo: https://klerith.github.io/claude-asteroids/

## Running

Open `index.html` directly in a browser, or serve the folder:

```bash
npx serve .   # then visit http://localhost:3000
```

## Architecture

All game logic lives in `game.js`, loaded by `index.html` as a classic (non-module) script against an 800×600 `<canvas id="canvas">`. Everything is module-level globals; `W`/`H` in `game.js` must match the canvas size in `index.html`.

- **Loop**: `requestAnimationFrame(loop)` computes `dt` in seconds (clamped to 0.05) and calls `update(dt)` then `draw()`. All motion is `dt`-scaled (px/s, rad/s).
- **State machine**: global `state` is `'playing' | 'dead' | 'gameover'`. `update()` branches on it: `'dead'` runs a respawn timer (`deadTimer`) while asteroids/particles keep moving; `'gameover'` waits for Space to call `initGame()`. Level advances via `nextLevel()` when `asteroids` is empty (spawns `3 + level` large asteroids, away from center via `SAFE_DIST`).
- **Entities**: `Ship`, `Bullet`, `Asteroid`, `Particle` classes each implement `update(dt)` and `draw()` and use a `dead` flag; the main loop removes dead entities with `filter`. Collisions are circle-vs-circle via `dist()` in `update()`.
- **Toroidal space**: positions are wrapped with `wrap()` (except particles).
- **Asteroid sizes**: `size` 3/2/1 = large/medium/small, indexed into the `RADII`, `SPEEDS`, `POINTS` arrays (index 0 unused). `split()` returns two asteroids of `size - 1`.
- **Input**: `keys[code]` holds held state; `pressed(code)` is a consume-once edge trigger backed by `justPressed` (used for Space to shoot/restart).

## Conventions

- UI text and code comments are in Spanish (e.g. HUD shows `NIVEL`, `PUNTAJE`); keep it that way.
- Code sections are separated with `// ── Name ───…` banner comments.
- The README still mentions power-ups and a shooting-star asteroid, but those were removed from the code (commit `13e713f`).
