# Sokoban — AI Puzzle Solver

A retro, pixel-art Sokoban game with a built-in AI that solves the puzzles for you or gives you a hint.

**▶ Play it: https://harshilmayurpatel.github.io/Sokoban-AI-Solver/**

Push every crate onto a glowing target. If you get stuck, the AI searches the puzzle's state space and shows your next push, or plays the whole level out.

## Features

- 12 original levels, each verified solvable by the solver, ordered from 2 to 18 pushes
- **Hint** shows the walking path and an arrow on the crate to push next
- **AI Solve** plays the solution out move by move
- Three search algorithms to switch between: **A\*** (default), **Depth-First Search** and **Breadth-First Search**
- Undo, restart, move and push counters, best score per level
- Chiptune sound effects, CRT scanlines, smooth pixel animation
- Plays on phones with swipe or an on-screen D-pad

## How the solver works

[`solver.js`](solver.js) treats the puzzle as a graph search over **push states**. The player's exact square doesn't matter, only which area it can reach, so walking moves are collapsed into a single node. That shrinks the search space a lot.

- **State:** the sorted crate positions plus the player's reachable area, stored as its smallest cell index
- **Moves:** every crate push the player can reach from the current area
- **Dead-square pruning:** before searching, the solver pulls crates backwards from every goal. Any square a crate can never travel from to a goal (corners, dead-end walls) is marked dead, and pushes onto it are skipped.
- **Freeze deadlocks:** pushes that lock a 2×2 block of crates and walls with a crate off its goal are pruned
- **Heuristic:** the sum of each crate's push distance to its nearest goal. DFS uses it to order children and A\* uses it as its estimate.
- **Algorithms:**
  - **DFS** finds a valid solution quickly, but the solution can wander
  - **BFS** finds the fewest pushes
  - **A\*** finds short solutions while exploring far fewer states
- Once a solution is found, each push is expanded into walking moves with a BFS path, giving a standard move string (`lurd` = walk, `LURD` = push).

The solver runs in a **Web Worker**, so the game never freezes while it thinks.

## Controls

| Key | Action |
| --- | --- |
| Arrows / WASD | Move |
| Z / U / Backspace | Undo |
| R | Restart level |
| H | Hint |
| P | AI solve (press again to stop) |
| I | Switch solver (A\* → DFS → BFS) |
| M | Sound on/off |
| Esc | Level select |

## Run locally

No build step or dependencies. Serve the folder (a server is needed for the Web Worker):

```bash
python -m http.server 8000
```

Then open http://localhost:8000.

## Files

| File | What it is |
| --- | --- |
| `index.html` | Page, styles, HUD and overlays |
| `game.js` | Game loop, rendering, input, sound |
| `solver.js` | Search algorithms and deadlock detection (also runs as the worker) |
| `levels.js` | Level set in standard XSB format |

---

Part of [Harshil Patel's portfolio](https://harshilmayurpatel.github.io/#projects).
