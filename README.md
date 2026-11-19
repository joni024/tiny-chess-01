# Tiny Chess — a 4 KB chess engine

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/joni024/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/joni024/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

Entry for the [make.fun](https://make.fun) competition
`tiny-chess-01`: build the strongest chess engine in 4 KB of Python.

## Approach

`main.py` (4,092 bytes) is an LZMA-compressed, base85-encoded engine
(allowed by the rules) that unpacks and runs a single-file engine,
Python 3.13 stdlib only, fully deterministic (fixed node budget, no clock,
no randomness).

- **Board:** 0x88, incremental Zobrist hashing, make/unmake with full
  legality check (perft-validated against python-chess).
- **Search:** iterative deepening to a fixed 100,000-node budget,
  negamax alpha-beta with transposition table, principal-variation
  search at the root, quiescence search (captures + promotions, full
  evasions in check, delta pruning), null-move pruning (R=2),
  late-move reductions, check extensions, MVV-LVA + killer +
  transposition-move ordering, repetition detection (draw score).
- **Eval:** material + piece-square tables (explicit pawn/king tables,
  centralisation formulas for N/B/R/Q), king centralisation in the
  endgame (when non-pawn material is low).
- **Protocol:** talks to the gauntlet env over HTTP (`/game`, `/move`);
  replays the move list from the start position each turn.

## Local practice results (6 games vs the house ladder each)

- trial-0: 3.0/6, Elo **1279** (beat depth-2, depth-3, nodes-100k)
- trial-1: 2.5/6, Elo **1191** (beat depth-2, depth-3; drew depth-4)
- trial-2: 4.0/6, Elo **1451** (beat depth-2, depth-3, depth-4,
  nodes-500k by adjudication)
- **Mean practice Elo: 1307** (final threshold is 1154).
