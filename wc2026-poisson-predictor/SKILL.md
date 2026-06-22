---
name: wc2026-poisson-predictor
description: >
  Predict FIFA World Cup 2026 match outcomes and simulate full tournaments
  using Poisson regression on Elo ratings with Monte Carlo methods.
  Covers head-to-head win/draw/loss probabilities, expected goals,
  group-stage simulation, knockout brackets, and tournament-winner odds
  for all 48 qualified teams. Built on the football_WorldCup_2026_predictions
  engine by Anas Riad.
---

# World Cup 2026 Poisson Match Predictor

Predict any FIFA World Cup 2026 match result or simulate the entire
104-match tournament using a Poisson regression model trained on
historical international football data with pre-match Elo ratings.

**Provenance**: public project at
<https://github.com/anesriad/football_WorldCup_2026_predictions>
(16 stars, active as of June 2026). The original repository contains
Jupyter notebooks, trained Poisson models, Python source modules, and a
Streamlit app but no SKILL.md. This skill was shaped from that source.

## When to use

- User wants to predict the outcome of a specific World Cup 2026 match
  (e.g. "Who wins Brazil vs Germany?")
- User wants win/draw/loss probabilities for a head-to-head matchup
- User wants expected goals for both sides in a given fixture
- User wants to simulate a full World Cup 2026 tournament and see who
  wins most often across thousands of runs
- User wants stage-by-stage advancement probabilities (Round of 32,
  Round of 16, Quarterfinals, Semifinals, Final)
- User wants to compare two teams' tournament paths or group-stage
  chances
- User wants to understand how Elo ratings and Poisson models drive
  football predictions

## How the engine works

The prediction pipeline has these components:

1. **Elo ratings** -- pre-match Elo for every qualified team, built from
   historical results with recency weighting.
2. **Poisson regression** -- two separate models (home goals, away goals)
   trained on features: `home_elo_pre`, `away_elo_pre`,
   `tournament_weight`, `neutral` venue flag, plus intercept.
3. **Match prediction** -- given two teams, compute expected goals from
   the Poisson models, then derive score-matrix probabilities up to
   `max_goals` (default 10) to get win/draw/loss percentages.
4. **Group-stage simulation** -- simulate all group matches, build
   standings with FIFA tiebreakers (points, goal difference, goals
   scored), determine direct qualifiers and best third-placed teams.
5. **Knockout bracket** -- fill the Round-of-32 template from group
   results, simulate each knockout round (with extra time / penalties
   modeled as coin-flip tiebreakers) through to the Final.
6. **Monte Carlo tournament** -- repeat the full tournament N times
   (default 10,000) and aggregate how often each team reaches each stage.

Source modules:
- [`src/prediction.py`](https://github.com/anesriad/football_WorldCup_2026_predictions/blob/main/src/prediction.py) -- `predict_match()`, `simulate_match()`, `get_h2h_score()`
- [`src/group_stage.py`](https://github.com/anesriad/football_WorldCup_2026_predictions/blob/main/src/group_stage.py) -- group fixture prep, table updates, tiebreakers
- [`src/qualification.py`](https://github.com/anesriad/football_WorldCup_2026_predictions/blob/main/src/qualification.py) -- direct qualifiers + best third-placed logic
- [`src/bracket.py`](https://github.com/anesriad/football_WorldCup_2026_predictions/blob/main/src/bracket.py) -- Round-of-32 bracket template and filling
- [`src/knockout.py`](https://github.com/anesriad/football_WorldCup_2026_predictions/blob/main/src/knockout.py) -- knockout-stage simulation through Final
- [`src/tournament.py`](https://github.com/anesriad/football_WorldCup_2026_predictions/blob/main/src/tournament.py) -- full single-tournament simulation orchestrator
- [`src/data_loader.py`](https://github.com/anesriad/football_WorldCup_2026_predictions/blob/main/src/data_loader.py) -- dataset and model loading with caching
- [`simulation_app.py`](https://github.com/anesriad/football_WorldCup_2026_predictions/blob/main/simulation_app.py) -- Streamlit interactive app

Pre-trained models (pickle):
- `models/poisson_home.pkl` -- home-goals Poisson regression
- `models/poisson_away.pkl` -- away-goals Poisson regression
- `models/feature_columns.pkl` -- ordered feature column list

## Required environment

```
pip install pandas numpy scipy statsmodels scikit-learn streamlit plotly matplotlib
```

Python 3.10+. Clone the source repo to get trained models and reference
data:

```
git clone https://github.com/anesriad/football_WorldCup_2026_predictions.git
cd football_WorldCup_2026_predictions
pip install -r requirements.txt
```

## Inputs

| Input | Required | Description |
|---|---|---|
| Home team | Yes (for match prediction) | Country name as used in FIFA rankings (e.g. "Brazil", "Germany", "Korea Republic") |
| Away team | Yes (for match prediction) | Country name for the opponent |
| Neutral venue | No (default: true for WC) | Whether the match is on neutral ground |
| Simulation count | No (default: 10,000) | Number of Monte Carlo tournament runs |
| Task type | Yes | One of: `predict_match`, `simulate_tournament`, `compare_teams`, `explain_model` |

## Workflow

### 1. Identify user intent

Determine which task the user wants:

- **predict_match** -- user names two specific teams and wants
  probabilities, expected goals, or a predicted scoreline.
- **simulate_tournament** -- user wants full-tournament Monte Carlo
  results showing each team's chances at each stage.
- **compare_teams** -- user wants to compare two or more teams' overall
  tournament prospects or head-to-head records.
- **explain_model** -- user wants to understand the methodology, Elo
  system, Poisson approach, or data sources.

### 2. Run prediction

For **predict_match**:

```python
from src.prediction import predict_match

result = predict_match("Brazil", "Germany", neutral=True)
# Returns: expected home goals, expected away goals,
#          win/draw/loss probabilities, score matrix
```

Present: win %, draw %, loss %, expected scoreline, most likely exact
scores.

For **simulate_tournament**:

```python
from src.tournament import simulate_tournament

# Single run:
result = simulate_tournament()
# result["summary"]["winner"], result["group_tables"], etc.

# Monte Carlo (N runs):
from collections import Counter
winners = Counter()
for _ in range(10000):
    res = simulate_tournament()
    winners[res["summary"]["winner"]] += 1
```

Present: stage-by-stage probability table for all 48 teams, sorted by
tournament-win probability.

### 3. Format output

- For match predictions: show a clear probability breakdown with
  percentages, expected goals to one decimal, and the 3-5 most likely
  exact scorelines.
- For tournament simulations: show a ranked table with columns for
  R32 %, R16 %, QF %, SF %, Final %, Champion %.
- For comparisons: side-by-side probability profiles.
- Always cite the Elo ratings driving the prediction when relevant.

## Output

Return match predictions as structured probability breakdowns or
tournament simulation results as ranked probability tables. Include the
methodology note that predictions use Poisson regression on Elo ratings
from historical international match data.

## Limitations

- Elo ratings are pre-computed from historical data; real-time form,
  injuries, and squad changes are not reflected.
- The Poisson model assumes goal-scoring follows independent Poisson
  distributions; it does not capture tactical correlations or in-game
  momentum.
- Knockout tiebreakers (extra time / penalties) are modeled as
  probability-weighted coin flips, not as separate simulations.
- All World Cup matches are treated as neutral-venue; actual host-nation
  advantage (USA, Mexico, Canada) is not specially modeled beyond Elo.
- The 48-team format with 12 groups of 4 and a Round of 32 follows the
  announced FIFA structure as of early 2026.
