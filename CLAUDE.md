# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step. Open `index.html` directly in a browser, or serve locally:

```bash
npx serve .
# then visit http://localhost:3000
```

No package.json, no dependencies, no linter, no test suite.

## Architecture

Single-file vanilla JS game (`game.js`) on an 800×600 HTML5 Canvas. No modules, no bundler.

### Classes

- `Bullet` — projectile; dies after `ttl` seconds or on asteroid hit
- `Asteroid` — irregular polygon; size 3 splits into 2× size 2, size 2 into 2× size 1, size 1 dies
- `Ship` — player; has `invincible` countdown on respawn, `shootCooldown` rate-limiter
- `Particle` — explosion fragment; alpha fades as `ttl` drains

### Game state

Global vars: `ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state`, `deadTimer`.

`state` is a string enum: `'playing'` | `'dead'` | `'gameover'`.

### Game loop

`requestAnimationFrame` drives `loop(ts)` → `update(dt)` + `draw()`. `dt` is clamped at 50 ms to prevent spiral-of-death on tab-hide resume.

### Input

`keys{}` tracks held keys. `justPressed{}` tracks one-shot presses; `pressed(code)` consumes and resets the flag — prevents auto-fire on held Space.

### Collision detection

Circle vs circle (`dist()` < sum of radii). Ship collision uses `radius * 0.82` for a forgiving hitbox.

### Wrapping

`wrap(v, max)` wraps coordinates toroidally. All entities (ship, bullets, asteroids) call it every frame.

## Constants to know

| Symbol | Location | Meaning |
|--------|----------|---------|
| `RADII` | top of `game.js` | pixel radius per asteroid size |
| `SPEEDS` | top of `game.js` | base speed per asteroid size |
| `POINTS` | top of `game.js` | score per asteroid size |
| `W`, `H` | top of `game.js` | canvas dimensions (800×600) |
