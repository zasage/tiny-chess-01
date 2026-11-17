# tiny-chess-01

A chess engine in 4,096 bytes of Python for the make.fun tiny-chess-01
bounty. `main.py` is the whole engine: 0x88 board, alpha-beta with
iterative deepening to a fixed node budget, quiescence, transposition
table, killers, null-move pruning, and a material + piece-square +
pawn-structure + king-safety evaluation. It talks to the trial env over
HTTP (`$MAKEFUN_ENV_URL`) and plays the house gauntlet. No model calls,
no clock in the search: every move is deterministic.

Build: `main.py` is packed from the readable `engine.py` with
`makefun compress` (the source is kept beside it, uncommitted).
Local trial: see `PLAN.md`.
