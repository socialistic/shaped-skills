---
name: golazo-pool-optimizer
description: >
  Optimise exact-score picks for football forecasting pools using
  Dixon-Coles modelling, Shin de-vigging, conditional rarity bonuses,
  and Monte Carlo contest simulation. Turns bookmaker odds into
  calibrated scoreline probabilities, ranks picks by expected points,
  and decides when to spend the one-shot x2 multiplier.
  Built on the golazo engine by ohugonnot.
---

# Golazo — Football Pool Score Optimizer

Given bookmaker odds for a slate of football matches in a points-based
forecasting pool, recommend the highest-value exact-score pick per match,
decide whether to place the one-shot x2 multiplier, and optionally
simulate thousands of tournaments to maximise probability of finishing
first.

**Provenance**: public project at
<https://github.com/ohugonnot/golazo> (Python, MIT license, 75 unit
tests, zero third-party dependencies, active as of June 2026). The
original repository is a pure-stdlib CLI engine with no SKILL.md. This
skill was shaped from that source.

## When to use

- User has bookmaker 1X2 odds for upcoming football matches and wants
  the optimal exact-score forecast for a pool/contest
- User wants to understand which scoreline maximises expected points
  given the pool's quotation and rarity-bonus schedule
- User needs the x2-multiplier placement verdict for a multi-round
  tournament (when to spend it vs. wait)
- User wants Monte Carlo simulation of a full tournament to see which
  strategy maximises P(finish 1st) rather than just expected value
- User wants to see the de-vigged true probabilities behind bookmaker
  odds (Shin or power method)
- User is playing a World Cup 2026 forecasting pool and needs
  matchday-by-matchday optimal picks with contextual J3 adjustments

## How the engine works

Four self-contained Python modules, pure standard library:

1. **De-vigging** — strips the bookmaker overround from 1X2 odds using
   Shin's (1993) insider-trading model or the power method, recovering
   true outcome probabilities that respect the favourite-longshot bias.

2. **Goal intensities** — solves numerically for Poisson rates
   (λ_home, λ_away) whose Dixon-Coles scoreline matrix reproduces the
   de-vigged 1X2 probabilities. When an over/under line is available, it
   constrains the total intensity.

3. **Scoreline matrix & EV ranking** — builds the full Dixon-Coles
   P(i,j) matrix with the τ correction for 0-0, 1-0, 0-1, 1-1. For
   each candidate score:
   ```
   EV(s) = points(outcome(s)) · P(outcome(s)) + rarity_bonus(s) · P(exact = s)
   ```
   The rarity bonus is conditional: it depends on what share of
   correct-result players also picked that exact score (5-tier schedule
   from +20 common to +100 ultra-rare).

4. **x2 multiplier placement** — a patience-schedule + triple-convergence
   quality gate decides when to spend the irreversible one-shot doubler:
   - Timing: best match must stand out from its slate (ratio decays as
     tournament progresses: 1.8 → 1.4 → 1.15)
   - Quality: P(result) > 0.65, score ownership < 20%, fresh odds
   - Last window forces placement (unused x2 = 0 points)

5. **Monte Carlo tournament optimiser** (`optimize_winprob`) — simulates
   thousands of tournaments against a popularity-driven field model to
   maximise P(finish 1st) instead of expected value, because the two
   objectives diverge when trailing late.

6. **J3 stakes context** — adjusts group-stage matchday-3 fixtures for
   dead-rubber draws, rotation effects, and the 2026 48-team
   goal-difference incentive for third-placed teams.

Source modules:
- [`src/model.py`](https://github.com/ohugonnot/golazo/blob/main/src/model.py) — core pipeline: de-vig, λ solve, Dixon-Coles matrix, EV ranking, x2 verdict
- [`src/optimize_winprob.py`](https://github.com/ohugonnot/golazo/blob/main/src/optimize_winprob.py) — Monte Carlo P(1st) tournament optimiser
- [`src/odds_api.py`](https://github.com/ohugonnot/golazo/blob/main/src/odds_api.py) — pure-stdlib client for The Odds API, median aggregation across ~24 books
- [`src/stakes_context.py`](https://github.com/ohugonnot/golazo/blob/main/src/stakes_context.py) — J3 contextual adjustments (dead rubber, goal-diff chase)
- [`docs/methodology.md`](https://github.com/ohugonnot/golazo/blob/main/docs/methodology.md) — full mathematical derivations

## Input format

The engine reads JSON on stdin. Minimal input per match:

```json
{
  "gameweek": 1,
  "total_gameweeks": 9,
  "x2_holding": true,
  "matches": [
    {
      "id": "match_001",
      "home": "Brazil",
      "away": "Serbia",
      "odds_home": 1.45,
      "odds_draw": 4.20,
      "odds_away": 7.50,
      "total_hint": 2.5,
      "points_home": 22,
      "points_draw": 60,
      "points_away": 95,
      "field_bets": {"home": 0.82, "draw": 0.11, "away": 0.07}
    }
  ]
}
```

Key fields:
- `odds_home / odds_draw / odds_away` — decimal bookmaker 1X2 odds
- `total_hint` — over/under goals line (pins total intensity)
- `points_home / points_draw / points_away` — pool quotation per outcome
- `field_bets` (optional) — community split across 1X2 for ownership calc
- `gameweek / total_gameweeks / x2_holding` (optional) — enable x2 verdict

## Output

Per match: ranked candidate scores with EV, probability, rarity tier,
and the recommended pick. Per slate: x2 placement verdict with timing
gate, quality gate, and rationale. Tournament mode: P(1st) optimised
picks across all remaining rounds.

## Required environment

Python 3.x standard library only — no pip install needed.

## Running

```bash
# EV-optimal picks
python src/model.py < matchday.json

# Monte Carlo win-probability optimiser
python src/optimize_winprob.py < matchday.json

# Tests (75 deterministic, no network)
python -m unittest discover -s tests
```
