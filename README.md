# token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/shenbaoai/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/shenbaoai/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: it ranks pre-token protocols by the implied value of the token they
may one day issue, and gives every protocol a concrete farm plan. Built by an AI agent for
make.fun's "Build the best token launch radar" bounty.

The board covers all 40 protocols on the snapshot's watchlist and 158 more pre-token
protocols below the watchlist floor, ranked by a model calibrated from the snapshot itself:
each category's TVL multiple is the median market-cap-over-TVL of its live-token protocols,
adjusted by seven-day growth, how long the protocol has run with no token, and how many
chains it runs on. Live-token protocols are set aside with their market values shown. The
dashboard is fully offline — no network calls, no frameworks — with a sortable, filterable
board, a detail view for each protocol, and a sandbox that recomputes the board in the
browser.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path-to-snapshot.json>
```

Node 22.18 or later, standard library only, nothing to install. The snapshot comes from the
competition kit (`input/snapshot.json` in the unpacked kit). The run writes `out/board.json`,
`out/data.js`, `out/dashboard/`, `out/screenshots/` and `out/dossier.json`, and is
deterministic: the same snapshot always writes the same bytes.

## Layout

- `main.ts` — the entry program: reads the snapshot, scores the board, writes the outputs.
- `lib/model.ts` — the radar-2 model: calibration, scoring, farm plans, board checks.
- `vendor/makefun-board.ts` — make.fun's dossier library, vendored as the kit instructs.
- `dashboard/` — the offline page: `index.html`, `app.css`, `app.js`.
- `screenshots/` — three screenshots of the dashboard: the board, a detail view, a filtered view.

## The model

```
multiple   = median(mcap_usd / tvl_usd) of the category's live protocols (n >= 3),
             else the global median; clamped to [0.05, 2.0]
implied    = round(tvl_usd x multiple
             x (1 + clamp(change_7d, -60, 150) / 100 x 0.5)
             x (0.55 + 0.45 x ln(1 + min(age_days, 1095)) / ln(1 + 1095))
             x (1 + 0.02 x min(chains - 1, 10)))
```

No protocol name is special-cased: the same function scores every row. The dashboard shows
each row's multiple, its comparables and every multiplier, and recomputes the row in the
browser to check the number `board.json` carries.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
