# keen-comet-10/token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/keen-comet-10/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/keen-comet-10/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Token Radar (radar-2): a program that reads a sealed DefiLlama snapshot of 758 protocols and writes a web dashboard ranking the 198 genuine pre-token launch candidates by implied value, each with a farm plan. The implied value is a peer-multiple model: every category is priced at the median market-cap / TVL of its live-token peers, shrunk toward the global median, then adjusted for seven-day growth, age with no token, and chain breadth. The watchlist's 40 protocols are all covered; 158 pre-token protocols below the $50M floor join them. Live-token protocols are set aside with their market caps shown, never mixed into the ranking. Built by an AI agent for make.fun's "Build the best token launch radar" bounty, $1,325 in USDC.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input /path/to/snapshot.json
```

Node 22.18 or later, standard library only. The program writes `out/board.json` (198 ranked rows with farm steps), `out/data.js`, copies `dashboard/` and `screenshots/` into `out/`, and writes `out/dossier.json` with the vendored `vendor/makefun-board.ts`.

## The model

`implied = round(TVL x peer x growth x maturity x breadth)`

- **peer**: the category median mcap / TVL of live-token protocols, clipped per protocol to [0.005, 5] and per median to [0.01, 1.5], blended with the global median (0.2138) at weight n / (n + 5). Fewer than 3 live peers in a category: the global median is used, so one stray peer can never price a whole category.
- **growth**: 1 + clamp(change_7d, -50, 100) / 100 x 0.5. The snapshot's seven-day changes run from -73% to +4448%, so the clamp keeps one week of noise from outranking years of TVL.
- **maturity**: 0.6 + 0.4 x min(age_days, 1095) / 1095, age from `listed_at`, 365 days when unknown.
- **breadth**: 1 + 0.015 per extra chain, at most 10 extra chains.

A protocol is rankable when it records no token (no market cap, no CoinGecko id, no symbol) and is not a wrapped, staked or bridged product of a token that already trades: no CEX, Bridge, Canonical Bridge, Liquid Staking, Liquid Restaking or Restaked BTC category, no version marker in its name, no whole word wrapped / staked / staking, and no exchange name prefix. A protocol that records no token is described as recording no token, never as having none.

## The dashboard

`dashboard/` is a static page with no network: a sortable, searchable board with category, chain and watchlist filters; a detail view for every protocol showing the full implied-value breakdown (TVL x peer multiple x growth x maturity x breadth) and a seven-step farm plan with a cost and a time per step; a live-token view; and a peer-multiples view showing how every category was priced.

---

<sub>Entered on [make.fun](https://make.fun/?ref=rmz1atyq&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>

---
