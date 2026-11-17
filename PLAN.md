# tiny-chess-01 — plan

Engine: 0x88 board, negamax alpha-beta + iterative deepening to a fixed
node budget (deterministic, no clock), quiescence, transposition table,
killers, history, null-move, LMR, futility at depth 1. Eval: material
+ piece-square + bishop pair +25 + pawn structure + rook 7th/open file
+ king pawn-shield, tapered king MG/EG. HTTP loop over $MAKEFUN_ENV_URL,
UCI moves, 10 s/move, 1.800 s/trial.

## Practice Elo (6 games vs depth-2, depth-3, depth-4, nodes-100k/250k/500k)
- v10 (ND0 60.000 CUMULATIVE/move): trial-0 **1.279** (3W 0D 3L),
  trial-1 876, trial-2 1.099. Floor 1.154 is read from trial-0 only.
  Max think 3.438 ms (t0), 3.429 (t1), 3.681 (t2); ZERO moves >4 s
  in 847 moves, ZERO time losses. Change vs v9: N reset once per
  move (top of root()), not per depth; ND0 = TOTAL nodes per move.
- v9 (ND0 28.000/depth): trial-0 **1.279** (2W 2D 2L), trial-1 997,
  trial-2 1.099. REJECTED by server 2026-11-17: practice grade 876
  (1.0 pt), all other checks PASS. Cause (proven): time forfeits.
  Local t0 think_ms max 9.260, 3 moves >8 s (g1 8.336, g4 8.021+
  7.796, g6 9.260+5.949); engine is deterministic (no clock), so only
  wall-clock timing can differ; server (1 CPU) evidently spikes
  ~2.3x on some moves. RULES.txt: a move near 10 s "can fall on the
  other side of the limit on the replay".
- Alternatives measured + rejected: ND0 16.000/depth t0 1.191, max
  6.410 ms; cumulative 80.000 t0 876 (1W 0D 5L), max 4.548 ms.
  60k is the largest budget whose slowest move stays ~3.4 s locally
  (3.4x2.3=7.8<10 s even at the observed server spike).
- Earlier: v1 997, v2 708, v4 (30k) 1.191/997, v5 (40k) 1.191/1.099
  but 4 time losses in trial-1, v6 (25k) 1.099, v7/v10 rejected.
- Key bug found: TT flag stored backwards (fail-high as EXACT); fix +200.
- Node budget is capped by the slowest move: 28k -> max think 9.387 ms
  (trial-0). Speed: SQ list + combined CT=V+PST table.

## Packaging
main.py = `makefun compress engine.py --out main.py` (own command,
after the source is written). Tree <=8.192 B, each file <=4.096 B.
CLI: node <kit>/cli/makefun.mjs. Source engine.py stays uncommitted
(bundle sends it via the compress record); only main.py is committed.
