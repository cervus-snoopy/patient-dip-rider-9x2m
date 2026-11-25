[![make.fun](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![make.fun](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/cervus-snoopy/patient-dip-rider-9x2m/rank.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![make.fun](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/cervus-snoopy/patient-dip-rider-9x2m/reward.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)

# Patient Dip Rider — Tiny Bot, Easy $2: Beat Buy-and-Hold on Solana

A small mean-reversion bot for hourly Solana bars (`bars-replay/v1`). It buys
only deep, slow dips and sits in cash the rest of the time.

## Strategy

- **Entry:** RSI(14) below 25 **and** the close below the lower Bollinger band
  (20 closes, 2 population sigmas). Both legs must fire. Size: equity / 7.
- **Exit:** RSI(14) above 55, or +5% take-profit, or −4% stop.
- **Probe leg:** from batch 36, while filled trades trail the 8-trade
  guardrail and nothing is held, it buys $300 of the most oversold symbol
  (lowest RSI under 60) to keep the entry compliant. Exits at RSI 55, +3%,
  −3%, or after 8 bars.

## Why this shape

The dev window (29 Oct–3 Nov 2026) falls almost monotonically: SOL −8.7%,
every asset −3% to −14%, correlations 0.71–1.00. Buy-and-hold loses $869 on
$10,000; every house trend/momentum baseline loses $20–$268. This bot is the
only tested shape that ends positive on the dev window.

Tested offline against the kit's frozen dev window with the kit's fill model
(next-open fill, 10 bps fee, 5 bps slippage, constant-product impact):

| Shape | Dev window PnL | 72-bar rolling mean | Beats B&H |
| --- | --- | --- | --- |
| Buy-and-hold | −$869 | −$252 | — |
| EMA 8/21 cross (house) | −$173 | −$80 | 44/65 |
| Momentum top-3 (house) | −$268 | −$163 | 42/65 |
| MACD+CCI (house, best) | −$20 | — | — |
| **This bot** | **+$282** | **+$88** | **58/65** |

Momentum rotation, EMA crossovers, breakouts, faster RSI legs and weaker dip
tiers were all tested and all lose on this data: the market mean-reverts at
12–24h horizons, so chasing strength buys tops. The probe leg costs ~$2–$6
of PnL on average and takes sub-8-trade windows from 17/65 (falling) and
65/65 (rally) to 0/65 in both regimes.

## Run

Python 3.13, standard library only. The grader runs `python3 -P -s main.py`;
the bot speaks `agent-fun/bars-replay/v1` on stdin/stdout via the vendored
`vendor/makefun_bars.py` (a byte-identical copy of the kit's
`sdk/makefun_bars.py`).

```sh
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot . --driver process
```

## Files

- `main.py` — the bot (entrypoint)
- `vendor/makefun_bars.py` — the kit's bars-replay shim, byte-identical
- `LICENSE` — MIT
