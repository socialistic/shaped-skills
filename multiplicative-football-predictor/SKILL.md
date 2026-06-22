---
name: multiplicative-football-predictor
description: |
  Predict football match outcomes across worldwide leagues using a
  multiplicative rating model with Poisson match simulation. Covers
  team rating estimation via LP optimization, expected goals, win/draw/loss
  probabilities, and multi-league support from EPL to Hong Kong Premier
  League to AFC Champions League. Built on tanjt107/football-prediction.
provenance:
  skill_url: https://github.com/tanjt107/football-prediction
  source_url: https://github.com/tanjt107/football-prediction
  note: >
    Shaped from a public GCP-based football prediction pipeline (17 stars).
    The original repo has no SKILL.md; this skill wraps the project's
    multiplicative rating + Poisson simulation methodology as a
    conversational workflow.
---

# Multiplicative Football Rating & Prediction

Predict football match outcomes using a multiplicative team-rating model
with Poisson goal simulation, supporting worldwide leagues from the English
Premier League to the Hong Kong Premier League.

**Source**: [tanjt107/football-prediction](https://github.com/tanjt107/football-prediction)

## Job

Given two teams (or a fixture list from any supported league), produce:

1. **Team offensive/defensive ratings** via LP optimization that minimizes
   the difference between forecasted and actual goals.
2. **Expected goals** for each side using the formula:
   `home_goals = avg_goals + home_advantage + home_off_rating + away_def_rating`
3. **Win/Draw/Loss probabilities** via Poisson distribution on expected goals.
4. **League standings simulation** when a full fixture round is supplied.

## Required Inputs

- **Match fixture** -- at minimum two team names from the same league;
  optionally a CSV of fixtures with `home_team`, `away_team` columns.
- **League context** -- which league/competition the teams belong to. The
  model covers: English Premier League, Championship, La Liga, Bundesliga,
  Ligue 1, Serie A, J1 League, Chinese Super League, China League One,
  Hong Kong Premier League, AFC Champions League, AFC Cup, UEFA Champions
  League, FIFA Club World Cup, Asian Cup Qualification, WC Qualification
  Asia.
- **Optional overrides** -- recent form notes, injury updates, or xG data
  to refine the base ratings.

## Modeling Methodology

The source implements Wayne Winston's multiplicative rating approach
(Coursera: Math behind Moneyball) combined with FiveThirtyEight-style
soccer prediction principles:

### Rating Estimation

Team ratings are computed using `pulp` (Python LP solver):
- **Offensive rating**: how many more goals a team scores than average
- **Defensive rating**: how many fewer goals a team concedes than average
- The solver minimizes `|forecasted_goals - actual_goals|` across all
  league matches

### Goal Adjustments

- Late goals (after 70th minute) by the leading team are linearly devalued
  to reduce noise from dead-rubber scoring
- Expected goals (xG) data is incorporated where available from Footy Stats

### Match Simulation

- Goals are drawn from independent Poisson distributions using each team's
  expected goals
- Home advantage is baked into the expected-goals formula
- Match probabilities (W/D/L) are computed from the joint Poisson PMF

### Team Strength Scale

Ratings are converted to a 0--100 percentage-of-possible-points scale with
tiers: Elite (90+), Very Strong (80--89), Strong (70--79), Above Average
(60--69), Average (50--59), Below Average (40--49), Weak (30--39), Very
Weak (<30).

## Data Sources

- [Footy Stats API](https://www.footystats.org/) -- match statistics, xG,
  league tables
- [HKJC](https://www.hkjc.com/) -- Hong Kong Jockey Club odds for
  calibration and value-bet detection

## Key Source Files

| Path | Purpose |
|---|---|
| [`src/function/solver/solver/solver.py`](https://github.com/tanjt107/football-prediction/blob/main/src/function/solver/solver/solver.py) | LP-based team rating solver |
| [`src/function/solver/solver/models.py`](https://github.com/tanjt107/football-prediction/blob/main/src/function/solver/solver/models.py) | Rating model data structures |
| [`src/function/simulate_tournament/simulation/models/match.py`](https://github.com/tanjt107/football-prediction/blob/main/src/function/simulate_tournament/simulation/models/match.py) | Poisson match simulation |
| [`src/function/simulate_tournament/simulation/models/team.py`](https://github.com/tanjt107/football-prediction/blob/main/src/function/simulate_tournament/simulation/models/team.py) | Team model with ratings |
| [`src/function/simulate_tournament/simulation/tournaments/`](https://github.com/tanjt107/football-prediction/tree/main/src/function/simulate_tournament/simulation/tournaments) | Tournament simulation (groups, knockout, season) |
| [`src/bigquery/sql/functions/matchProbs.js`](https://github.com/tanjt107/football-prediction/blob/main/src/bigquery/sql/functions/matchProbs.js) | BigQuery UDF for match probability calculation |
| [`assets/leagues.csv`](https://github.com/tanjt107/football-prediction/blob/main/assets/leagues.csv) | Supported leagues and configuration |

## Output Contract

Return a structured prediction containing:

```json
{
  "home_team": "Team A",
  "away_team": "Team B",
  "league": "English Premier League",
  "home_off_rating": 0.15,
  "home_def_rating": -0.08,
  "away_off_rating": 0.10,
  "away_def_rating": 0.05,
  "expected_home_goals": 1.62,
  "expected_away_goals": 1.18,
  "home_win_pct": 0.42,
  "draw_pct": 0.26,
  "away_win_pct": 0.32,
  "most_likely_score": "1-1",
  "rating_tier_home": "Strong",
  "rating_tier_away": "Above Average"
}
```

When multiple fixtures are supplied, return an array of predictions plus
aggregated league standings if applicable.
