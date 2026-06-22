---
name: dixon-coles-match-predictor
description: >
  Predict Premier League match outcomes using a time-decayed Dixon-Coles
  bivariate Poisson model. Estimates team attack/defence strengths via MLE
  on 5 seasons of EPL data (2020-2025), applies low-scoreline rho correction,
  and outputs a full scoreline probability matrix with 1X2 probabilities,
  expected goals, and value-bet identification against bookmaker odds.
  Built on KevinK657/Dixon-Coles-Implementation (Python, scipy).
requires:
  - Python 3.10+
  - numpy, scipy, pandas
---

# Dixon-Coles Match Predictor

> Provenance: shaped from
> [KevinK657/Dixon-Coles-Implementation](https://github.com/KevinK657/Dixon-Coles-Implementation)
> (Python). The original repo has no SKILL.md; this skill wraps its
> Dixon-Coles model fitting and match simulation into one agent workflow.

## When to use

Use this skill when the user needs:

- **Single match prediction** -- two Premier League teams in, full probability
  breakdown out: home win / draw / away win %, expected goals per side, and a
  scoreline probability matrix up to 10-10.
- **Value-bet detection** -- compare model probabilities against bookmaker
  implied odds and flag matches where the model edge exceeds a threshold
  (default 5%).
- **Parameter inspection** -- view fitted attack/defence strengths, home
  advantage, and rho correction for every EPL team.
- **Backtest over a matchweek window** -- run the model on held-out gameweeks
  and report accuracy, log-loss, and P&L on value bets.

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| Home team name | Yes | EPL team, e.g. "Arsenal" |
| Away team name | Yes | EPL team, e.g. "Chelsea" |
| Bookmaker odds (H/D/A) | Optional | Decimal odds for value-bet comparison |
| xi (time-decay rate) | Optional | Default 0.00325; higher = more recency bias |
| Matchweek CSV | Optional | For batch prediction or backtest |

## Output contract

### Match prediction (stdout)

```
Arsenal vs Chelsea
Home win:  52.3%  |  Draw: 24.1%  |  Away win: 23.6%
Expected goals   Arsenal 1.62 - 1.08 Chelsea
Most likely      1-0 (14.2%), 1-1 (11.8%), 2-1 (10.5%)
Over 2.5: 48.1%  |  BTTS: 46.3%
```

### Value-bet detection (when odds supplied)

```
Model edge vs market:
  Home win  model 52.3%  market 47.6%  edge +4.7%  -- below threshold
  Away win  model 23.6%  market 28.6%  edge -5.0%  -- VALUE: back Away
```

### Parameter table (on request)

Attack and defence strength, home advantage, and rho for every team in the
training set.

## Workflow

1. **Parse the user's request** -- identify teams, optional odds, mode
   (single match / batch / backtest / inspect params).
2. **Build or load the dataset** -- concatenate EPL season CSVs from
   `data/raw/`, compute `time_diff` days from the most recent match.
   Entry point: `scripts/build_dataset.py`.
3. **Fit the Dixon-Coles model** -- MLE via `scipy.optimize.minimize` with
   attack, defence, rho, and home-advantage parameters. Time-decay weight
   `exp(-xi * t)` down-weights older fixtures. Entry point:
   `scripts/fit_model.py` or directly `src/dc_model.solve_parameters()`.
4. **Simulate the match** -- `src/dc_model.dixon_coles_simulate_match()`
   produces an (11x11) scoreline probability matrix with rho correction
   on the 0-0 / 0-1 / 1-0 / 1-1 cells. Aggregate to 1X2 via
   `get_1x2_probs()`.
5. **Compare with odds** (if provided) -- convert decimal odds to implied
   probabilities, compute edge, flag value bets where edge > threshold.
6. **Return structured output** -- probabilities, scorelines, expected
   goals, and any value-bet flags.

## How the model works

1. **Bivariate Poisson** -- each team's goals follow a Poisson distribution:
   - Home expected goals: `exp(attack_home + defence_away + home_adv)`
   - Away expected goals: `exp(attack_away + defence_home)`
2. **Dixon-Coles correction** -- MLE-estimated `rho` parameter adjusts the
   joint probability of low-scoring outcomes (0-0, 0-1, 1-0, 1-1) to
   correct the independent-Poisson assumption.
3. **Time decay** -- each historical match is weighted by `exp(-xi * t)`
   where `t` is match age in days. Default xi = 0.00325 gives a half-life
   of ~213 days.
4. **MLE fitting** -- all parameters (n_teams attack + n_teams defence +
   rho + home_adv) solved jointly with a sum-to-n constraint on attacks.

## Key source files

- [`src/dc_model.py`](https://github.com/KevinK657/Dixon-Coles-Implementation/blob/main/src/dc_model.py)
  -- core model: `solve_parameters`, `dixon_coles_simulate_match`,
  `get_1x2_probs`, `rho_correction`
- [`scripts/build_dataset.py`](https://github.com/KevinK657/Dixon-Coles-Implementation/blob/main/scripts/build_dataset.py)
  -- data concatenation and time-diff computation
- [`scripts/fit_model.py`](https://github.com/KevinK657/Dixon-Coles-Implementation/blob/main/scripts/fit_model.py)
  -- model fitting entry point with xi search
- [`scripts/predict_match.py`](https://github.com/KevinK657/Dixon-Coles-Implementation/blob/main/scripts/predict_match.py)
  -- single-match prediction CLI
- [`src/backtest.py`](https://github.com/KevinK657/Dixon-Coles-Implementation/blob/main/src/backtest.py)
  -- matchweek-by-matchweek backtesting
