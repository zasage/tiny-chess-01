# tiny-chess-01 — plan

Engine: 0x88 board, negamax alpha-beta + iterative deepening to a fixed
node budget (deterministic, no clock), quiescence (captures; all evasions
in check), transposition table, killers, history, null-move pruning,
LMR, futility at depth 1. Eval: material + piece-square + bishop pair
+25 + pawn structure (doubled -8, isolated -6, passed +8+12xadvance)
+ rook 7th +12 / open file +8 + king pawn-shield, tapered king MG/EG.
HTTP loop over $MAKEFUN_ENV_URL, UCI moves, 10 s/move, 1.800 s/trial.

## Practice Elo (6 games vs depth-2, depth-3, depth-4, nodes-100k/250k/500k)
- v9 (ND0 28.000): trial-0 **1.279** (2W 2D 2L), trial-1 997,
  trial-2 1.099. Mean ~1.125. Floor 1.154 is read from trial-0 only.
- Earlier: v1 997, v2 708, v4 (30k) 1.191/997, v5 (40k) 1.191/1.099
  but 4 time losses in trial-1, v6 (25k) 1.099, v7/v10 rejected.
- Key bug found: TT flag stored backwards (fail-high as EXACT); fix +200.
- Node budget is capped by the slowest move (worst nps ~2.900):
  28k -> max think 9.387 ms (trial-0), 4.1 ms (trial-1/2). 30k+ risks
  the 10.000 ms/move limit. Speed: SQ list + combined CT=V+PST table.

## Packaging
main.py = `makefun compress engine.py --out main.py` (own command,
after the source is written). Tree <=8.192 B, each file <=4.096 B.
CLI: node <kit>/cli/makefun.mjs. Source engine.py stays uncommitted
(bundle sends it via the compress record); only main.py is committed.
