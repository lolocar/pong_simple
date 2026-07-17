# Pong

A single-player and two-player Pong game built with a single HTML file. No server or build tools needed — just open `index.html` in any modern browser.

## How to Play

Open `index.html` in your browser, select your game mode, and click **Start**.

## Controls

### 1 Player (vs AI)

| Action | Key |
|--------|-----|
| Move up | `↑` (Arrow Up) |
| Move down | `↓` (Arrow Down) |
| Fast mode | Hold `+` while moving (Shift+=) |

### 2 Players

| | Player 1 (Left) | Player 2 (Right) |
|---|---|---|
| Move up | `W` | `↑` |
| Move down | `S` | `↓` |
| Fast mode | Hold `F` | Hold `+` |

A **Swap Controls** toggle in the menu swaps the key bindings between players.

### Global

| Key | Action |
|-----|--------|
| `ESC` | Pause / Resume |
| `SPACE` | Launch ball after a score |

## Game Rules

- First to **7 points** wins
- Ball speed increases slightly (3%) with each paddle hit, capped at 12 px/frame
- Ball deflection angle depends on where it hits the paddle (edges produce sharper angles)
- AI prediction uses a weighted tracking system with imperfection for fair difficulty

## Implementation Details

### Architecture

Everything lives in a single `index.html` file with three sections:

- **CSS** — Fire-themed styling for the page, overlay, buttons, and toggle switch
- **HTML** — Canvas element, score display, overlay menu, and info bar
- **JavaScript** — Complete game logic (no dependencies)

### Game Loop

```
requestAnimationFrame(gameLoop)
  → update()   // physics, collisions, AI, particles, embers
  → draw()     // render everything to canvas
```

### Key Components

| Function | Responsibility |
|----------|---------------|
| `gameLoop()` | Main loop, calls update/draw each frame |
| `update()` | Ball physics, wall/paddle collisions, scoring, AI, particles |
| `draw()` | Renders background, embers, paddles, ball, labels, overlays |
| `updateKeyboardPaddles()` | Reads `keysDown` state and moves both paddles |
| `movePaddle(target, up, down, fastKey)` | Moves one paddle, returns whether fast mode is active |
| `updateAI()` | Predictive ball tracking with weighted smoothing |
| `handlePaddleHit(p)` | Calculates deflection angle, increases speed, triggers effects |
| `spawnHitParticles(x, y)` | Creates 12 spark particles on paddle contact |

### State Tracking

| Variable | Purpose |
|----------|---------|
| `gameMode` | `'1p'` or `'2p'` |
| `controlsSwapped` | Whether 2P controls are flipped |
| `keysDown` | Object tracking currently held keys |
| `hitTimer` | Frames remaining for hit flash effect |
| `lastHitPaddle` | Which paddle was last hit (for correct flash rendering) |
| `fastGlowP1` / `fastGlowP2` | Per-paddle fast mode glow timer |
| `speed` | Current ball speed (increases during rallies) |

### Constants

| Constant | Value | Meaning |
|----------|-------|---------|
| `WINNING_SCORE` | 7 | Points needed to win |
| `PADDLE_WIDTH` | 12 | Paddle thickness in px |
| `PADDLE_HEIGHT` | 90 | Paddle length in px |
| `BALL_RADIUS` | 5 | Ball size in px |
| `BALL_SPEED` | 6 | Starting ball speed |
| `MAX_SPEED` | 12 | Maximum ball speed |
| `AI_SPEED` | 4.2 | AI paddle movement speed |
| `AI_REACTION` | 0.08 | AI prediction weight (lower = slower reaction) |
| `P2_SPEED` | 6 | Keyboard paddle movement speed |
| `FAST_MULT` | 1.8 | Fast mode speed multiplier |

### Visual Effects

- **Fire theme** — Dark charcoal background gradient, orange paddles, warm yellow ball
- **Rising embers** — 40 ambient particles that float upward with slight horizontal drift
- **Hit flash** — Ball and hit paddle turn white for 6 frames on contact
- **Spark particles** — 12 orange sparks burst from the ball on each paddle hit
- **Fast mode glow** — Keyboard-controlled paddle gets a golden glow when fast mode is active
- **Glow halos** — Paddles and ball use canvas `shadowBlur` for a soft fire glow

### AI Behavior

The AI predicts where the ball will arrive by extrapolating its current trajectory:

```js
timeToArrive = (paddle2.x - ball.x) / ball.vx
predictedY = ball.y + ball.vy * timeToArrive
```

It then moves toward that position using a weighted average (`AI_REACTION = 0.08`), which makes it track imperfectly and gives the player a chance to win. The AI also ignores the ball when it's moving away (ball.vx ≤ 0).

## File Structure

```
mypong/
├── index.html   # Everything: HTML + CSS + JS
└── README.md    # This file
```

## Browser Compatibility

Works in any modern browser with Canvas 2D support (Chrome, Firefox, Safari, Edge).
