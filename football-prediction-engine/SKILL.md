---
name: football-prediction-engine
description: >
  Backtested football match predictor using weighted Poisson regression with
  Dixon-Coles correction, plus StatsBomb team-style and player analytics.
  Predicts win/draw/loss, expected goals, scoreline distributions, over/under,
  BTTS, and runs Monte Carlo World Cup title simulations. 59% accuracy on
  2,542 unseen matches (0.884 log-loss). Python, zero-config data download.
requires:
  - Python 3.9+
  - pip install -r requirements.txt
---

# Football Prediction Engine

> Provenance: shaped from
> [dennis20413/football-prediction-engine](https://github.com/dennis20413/football-prediction-engine)
> (Python, MIT). The original repo has no SKILL.md; this skill wraps its
> prediction and analytics pipelines into one coherent agent workflow.

## When to use

Use this skill when the user needs:

- **Single match prediction** -- two national teams in, full probability
  breakdown out: win/draw/loss %, expected goals, most-likely scorelines,
  over/under 2.5, both-teams-to-score, clean-sheet probabilities.
- **World Cup Monte Carlo simulation** -- title odds for every team by
  simulating thousands of tournaments, fixing already-played results and
  simulating the rest under the 48-team format.
- **Backtest validation** -- reproduce model accuracy on 2,542 held-out
  matches (59% accuracy, 0.884 log-loss, near-perfect calibration).
- **Team style analytics** -- StatsBomb-derived fingerprints: possession %,
  pressing height, directness, counter threat, field tilt, set-piece share.
- **Player analytics** -- per-90 profiles: npxG, xA, progressive passes
  and carries, defensive actions.
- **Stylistic matchup analysis** -- interpretable counter-index comparing
  two teams' tactical styles with radar/heatmap visualization.

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| Two team names | For match prediction | e.g. "France" and "Spain" |
| None | For tournament sim | Uses current World Cup bracket |
| None | For analytics | Auto-downloads StatsBomb open data |

No API keys or manual data setup required. All data is fetched automatically.

## Output contract

### Match prediction (stdout)

```
France 28.1% | Draw 27.4% | Spain 44.5%
Expected goals  France 1.10 - 1.45 Spain
Total goals     most likely 3, 80% interval 1-5
Most likely     1-1 (13.0%), 0-1 (10.8%), 1-2 (9.0%)
Over 2.5 46.7%  |  BTTS 51.5%
```

### Tournament simulation (stdout)

Title-odds ranking per team from Monte Carlo trials, with market comparison.

### Analytics (files + stdout)

- `team_style.csv` -- style fingerprints per team
- `style_analysis.png` -- radar/heatmap visualization
- Per-90 player stats to stdout

## Workflow

1. **Parse the user's request** -- identify which pipeline applies.
2. **Run the appropriate entry point**:
   - `python predictor.py "TeamA" "TeamB"` -- single match prediction
   - `python run.py` -- full World Cup title-odds simulation
   - `python backtest.py` -- reproduce accuracy numbers
   - `python analytics.py` -- team style fingerprints
   - `python players.py` -- per-90 player profiles
   - `python matchup.py` -- stylistic matchup index + chart
3. **Return structured output** -- probabilities for matches, rankings for
   tournaments, tables and charts for analytics.

## How the model works

1. **Strength model** (`strength.py`): weighted Poisson regression --
   `log(xG) = intercept + attack[scorer] - defence[conceder] + home`,
   solved simultaneously across all teams with 2-year half-life time-decay
   and match-importance weighting.
2. **Dixon-Coles correction** (`predictor.py`): MLE-estimated rho parameter
   corrects independent-Poisson bias on low-scoring results (0-0, 1-0, 1-1).
3. **Scoreline matrix**: outer product of two Poisson distributions times DC
   correction; W/D/L, O/U, BTTS, and scoreline probabilities integrated from
   this single matrix.
4. **Tournament Monte Carlo** (`tournament.py`): fixes played results,
   simulates the rest under 48-team format (12 groups, top 2 + 8 best thirds,
   knockout), repeated thousands of times.
5. **Style metrics** (`analytics.py`, `players.py`): derived from StatsBomb
   event data -- possession, PPDA/press height, directness, field tilt.
6. **Stylistic edge index** (`matchup.py`): standardized style factors +
   football logic (press x build-up, counter x transition risk). Interpretable
   heuristic, not a win probability.

## Source links

- Repository: https://github.com/dennis20413/football-prediction-engine
- Predictor CLI: https://github.com/dennis20413/football-prediction-engine/blob/main/predictor.py
- Strength model: https://github.com/dennis20413/football-prediction-engine/blob/main/strength.py
- Tournament sim: https://github.com/dennis20413/football-prediction-engine/blob/main/tournament.py
- Backtest: https://github.com/dennis20413/football-prediction-engine/blob/main/backtest.py
- Analytics: https://github.com/dennis20413/football-prediction-engine/blob/main/analytics.py
- Player profiles: https://github.com/dennis20413/football-prediction-engine/blob/main/players.py
- Matchup analysis: https://github.com/dennis20413/football-prediction-engine/blob/main/matchup.py
