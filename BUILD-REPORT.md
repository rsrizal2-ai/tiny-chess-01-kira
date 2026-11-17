# BUILD-REPORT: tiny-chess-01

Engine commit: 982276a643f4932ae82418426fc29618d5cd7815
Built 2026-11-18 by a leaf-builder subagent, own original code (no sunfish
or other engine copied). The starter was read only to learn the env protocol.

## What was built
0x88-board chess engine in Python 3.13 stdlib only. Iterative-deepening
alpha-beta with PVS, fixed budget of 80,000 nodes/move (deterministic: no
clock, no randomness), transposition table, killer moves, null-move pruning
(R=2, guarded: depth>=3, not in check, material>2500, no consecutive nulls),
quiescence over captures/promotions/check evasions. Eval: material +
piece-square tables (char-encoded) + bishop pair + phase-blended endgame
king centralisation; eval/material/king squares maintained incrementally.
Env driver: POST /game, /move, /resign at MAKEFUN_ENV_URL; falls back to
the first legal move on any search error, resigns only if a move is refused
twice. `main.py` is an LZMA+base64 payload with its unpacker (allowed by
the rules; payload + unpacker count toward the cap).

## Files + bytes
- main.py 3899 (cap 4096)
- LICENSE 638 (0BSD)
- README.md 448
- BUILD-REPORT.md (this file)
- Repo total under the 8192 cap; no file over 4096.

## Local evidence (kit env + scorer, no Docker)
grade-local needs Docker images; instead the kit's own env server and
scorer were run directly (process driver, same code the grader seals):
perft suite (6 standard positions, depths to 4): ALL OK; make/unmake +
incremental-eval/material/king-square/bishop-count invariants: 14,266
random-game checks, 0 mismatches; mate-in-1 found; mate-defense test OK.
Practice trials (6 games, kit scorer):
- trial-0 v1 (60k nodes, no endgame term): Elo 997 (1W 1D 4L)
- trial-0 v2 (+ endgame kings): Elo 1099 (1W 2D 3L)
- trial-1 v3 (shipped): Elo 1451 (3W 2D 1L), 308 moves, avg 1.32s,
  max 2.55s per move (10s limit); trial 449s of 1800s wall
- trial-0 v3 (shipped, exact bytes): Elo 1191 (2W 1D 3L), 449s
No timeouts, crashes, or illegal moves in any trial. Floor is 1154;
baseline practice mean is 1365 (1364/1451/1279). v3 mean over 2 trials:
1321. Determinism: fixed node budget only; replay safety relies on the
per-move time margin (max 2.55s observed here).

## Known gaps
- Strength near but not clearly above the house baseline; the prize gate
  (mean Elo above baseline, 90% paired interval > 0) is not assured.
- No repetition/50-move handling inside search; no opening book; no
  history heuristic; single middlegame PST set (no tapered eval).
- grade-local --driver docker was NOT run (no Docker images pulled in
  this sandbox); validation is direct env+scorer execution instead.
- Phase 2: needs a fresh public GitHub repo (NOT the other agent's
  tiny-chess-01 repo), claim file, and transcript bundle from this
  session. Never push from here; local commits only.
