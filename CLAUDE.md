# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the Games

Open any HTML file directly in a browser — no build step, no server required:

```
open shooter.html
open tictactoe.html
```

Both files work via `file://` protocol. There is no package.json, no dependencies, and no build toolchain.

## Repository Structure

This is a collection of standalone browser games, each a **single self-contained HTML file** with embedded CSS and JS. This pattern is intentional — it avoids CORS issues with `file://`, maximises portability, and requires zero setup.

## shooter.html Architecture

The game is structured as a state machine driving a `requestAnimationFrame` loop:

**States:** `TITLE → PLAYING → LEVEL_COMPLETE → PLAYING` (×5 levels) → `WIN`, with `PLAYING → GAME_OVER → TITLE` on death.

**Key globals:**
- `player` — single object, holds position, HP, aim angle, fire timer, walk cycle, iframes
- `enemies` — flat array; dead enemies stay briefly (`alive=false`, `deadTimer`) for death animation, then are removed
- `bullets` — unified array for both player (`owner:'player'`) and enemy (`owner:'enemy'`) bullets
- `particles` — unified pool for all visual effects
- `camera` — soft-follow with additive screen shake (`shakeIntensity` decays 0.85× per frame)
- `LEVELS` — static array of 5 level descriptors with `worldSize`, colors, `waves[]`, and `obstacles[]`

**Coordinate systems:** All entities live in world space. `worldToScreen()` / `screenToWorld()` convert via `camera.x/y + shakeX/Y`, rounded to integers for pixel-art feel.

**Collision:** Brute-force AABB each frame. `resolveEntityObstacle()` pushes entities out of obstacle rects using minimum-overlap axis.

**Enemy AI states:** `patrol → chase → strafe` based on distance thresholds defined per type in `ENEMY_TYPES`. Boss fires 3 bullets per shot (spread pattern).

## Git / GitHub Workflow

Remote: `https://github.com/Faisalb7/top-down-shooter`

**This is a hard requirement:** after every meaningful unit of work — a new feature, a bug fix, a visual tweak, a refactor — commit and push immediately. Never leave completed work uncommitted. This ensures we can always revert to a known-good state and never lose progress.

```bash
git add <file>
git commit -m "$(cat <<'EOF'
Short summary of what changed and why

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
EOF
)"
git push
```

Rules:
- One logical change per commit — not bulk "misc changes" commits
- Commit message summary line should say *what* changed and *why*, not just *what*
- Always `git push` immediately after committing — a local-only commit is not a saved state
- If multiple independent changes were made in one session, split them into separate commits before pushing
