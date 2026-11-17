# tiny-chess-01

A chess engine in under 4 KB of Python: 0x88 board, iterative-deepening
alpha-beta to a fixed node budget, transposition table, killer moves,
quiescence (captures, promotions, check evasions), material + piece-square
evaluation. Deterministic: no clock, no randomness. Plays the gauntlet
through the env at `MAKEFUN_ENV_URL`. `main.py` is an LZMA-packed payload
with its unpacker; the packed bytes count toward the 4,096-byte cap.
