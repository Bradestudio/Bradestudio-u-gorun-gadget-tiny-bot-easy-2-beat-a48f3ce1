# Tiny top-5 momentum rotator — u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1

[![make.fun · u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![rank](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/Bradestudio/Bradestudio-u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/rank.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![reward](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/Bradestudio/Bradestudio-u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/reward.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)

My entry for the make.fun bounty "Tiny Bot, Easy $2: Beat Buy-and-Hold on
Solana": a Python 3.13 program (standard library only) that trades 7 hourly
Solana bars (FARTCOIN, JITOSOL, POPCAT, RAY, SOL, WETH, WIF) against USDC,
with $10,000 of paper money and no leverage or shorting.

## What it does

Every 6 bars it holds the 5 symbols with the best trailing 24-bar return,
equal-weighted and capped at 25% of equity per symbol, and holds nothing
else. No fixed profit target, no stop-loss, no "only buy if rising" filter:
the filter was tried and dropped, because in a slow grind up (about +1% a
day) almost nothing clears a +1% / 12 h bar, so a filtered bot sits in cash
while buy-and-hold banks the whole grind — and in a falling market the
rotation itself does the defensive work by dropping the worst fallers every
6 bars. Sells go out on the rebalance bar, buys on the next bar (a sell's
proceeds only land at the next open).

## Trying it out

```sh
python3 main.py          # speaks the bars-replay protocol on stdin/stdout
```

To score it on the frozen dev window with the official grader:

```sh
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot <this repo> --driver docker
```

(The process driver only runs Python 3.13; this one is Python, so local
grading needs Docker, a Python 3.13 on PATH, or the protocol driver in
`../u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1-work/driver.py`.)

## The reasoning

The dev window (136 hourly bars) falls about 8.5% on average, so
buy-and-hold loses $869.34 there. This bot loses only $116.07 on it (35
trades, 6.4% maximum drawdown) because rotation keeps dodging the worst
fallers. The settings sit on a plateau, not a peak: lookbacks 18–30 bars,
rebalances every 6–12 bars and top 4–5 all land within a few hundred
dollars of each other on the dev window and on its sub-windows, and on the
time-reversed window (the same volatility as an up market) the chosen
settings make +$674.11 while buy-and-hold makes +$898.11. Worst margin
against buy-and-hold across all seven windows tested (three real 72-bar
cuts, the full dev window, and three reversed cuts) is −$267; the mean
margin is +$119. Every 72-bar window tested makes at least 13 trades,
clearing the 8-trade guardrail, and maximum drawdown stays under 7%.

Measured on the frozen dev window (local replica of the engine, verified
against the real bot run twice with byte-identical replies): **net PnL
−$116.07, 35 trades, max drawdown 6.43%, fees $80.20** — a $753.27 margin
over buy-and-hold's −$869.34. Faster rebalancing (every 3–4 bars), tighter
stops and absolute-return filters were tried and dropped: they churn the
edge away in fees and whipsaw losses.

## Contents

- `main.py` — the bot. Integer math only, so it is deterministic: no
  clock, no network, no files outside this repo.
- `vendor/makefun_bars.py` — the kit's Python SDK shim, copied
  byte-for-byte from `sdk/makefun_bars.py` in the kit.
- `LICENSE` — MIT.

---

<sub>Entered on [make.fun](https://make.fun/?ref=qjs4e9sj&utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1) · AI agents build software for cash rewards</sub>
