---
name: sportsbet-ml-predictor
description: |
  Predict football match outcomes and simulate betting bankroll performance
  using scikit-learn classifiers (Random Forest, Logistic Regression, MLP,
  Decision Tree) trained on historical league data from football-data.co.uk.
  Covers five European top flights (England, France, Spain, Italy, Germany),
  configurable feature windows, value-betting filters, and multi-platform
  odds comparison. Built on clemsage/SportsBet.
provenance:
  skill_url: https://github.com/clemsage/SportsBet
  source_url: https://github.com/clemsage/SportsBet
  note: >
    Shaped from a public ML repo (6 stars, MIT license). The original repo
    has no SKILL.md; this skill wraps the project's end-to-end prediction
    and betting simulation pipeline as a conversational workflow.
---

# Football Match Prediction & Betting Simulation

Predict football match results (Home / Draw / Away) using scikit-learn
classifiers and simulate betting strategies across major European leagues,
powered by the pipeline from
[clemsage/SportsBet](https://github.com/clemsage/SportsBet).

## Provenance

- **Source repo**: https://github.com/clemsage/SportsBet
- **License**: MIT
- **Data source**: [football-data.co.uk](https://www.football-data.co.uk) —
  historical match results with bookmaker odds for England, France, Spain,
  Italy, and Germany across multiple divisions and seasons.
- **Original form**: Python CLI pipeline (`main.py`) with modular
  prediction and betting modules; no prior SKILL.md existed.

## User Job

Help the user predict football match outcomes for a chosen league and
season range, then optionally simulate a betting strategy to evaluate
whether the model's edge beats bookmaker margins over time.

## Inputs

The user provides:

- **Country and division** — one of England, France, Spain, Italy, or
  Germany, plus the division level (1 = top flight by default).
- **Season range** — two-digit start and end season years (e.g. `10` to
  `24` for 2010/11 through 2023/24).
- **Classifier** — any scikit-learn classifier name. Defaults to
  `RandomForestClassifier`. Also supports `LogisticRegression`,
  `MLPClassifier`, `DecisionTreeClassifier`, or any sklearn classifier.

Optional:

- **Match history length** — number of recent home/away matches to weight
  by bookmaker odds for form features (default 0; try 3–5).
- **Direct confrontations** — number of past head-to-head results to
  include (default 3).
- **Betting platform** — ticker among `B365`, `BW`, `IW`, `PS`, `WH`,
  `VC` (default `B365`).
- **Initial bankroll** and **stake per bet** for simulation (defaults
  100 and 1).
- **Value betting** — bet only when predicted probability × odds > 1.

## Workflow

### Step 1 — Identify the fixture or league scope

Confirm the user's target: a specific upcoming match, a full-season
backtest, or a what-if scenario. Map their request to:

- `country`, `division`, `start_season`, `end_season`

### Step 2 — Explain data and features

The pipeline pulls CSV data from football-data.co.uk per season. Each
match row yields features:

| Feature group | Per-team features |
|---|---|
| Season form | Matches played, average points/match, current ranking |
| Recent form (home/away) | Last N results weighted by bookmaker odds |
| Head-to-head | Outcomes of last K direct confrontations |

All features are split into home-team and away-team variants. Categorical
columns are one-hot encoded; numerical columns are standard-scaled via
`sklearn.compose.ColumnTransformer`.

Source: [`predictions.py`](https://github.com/clemsage/SportsBet/blob/main/predictions.py),
[`game.py`](https://github.com/clemsage/SportsBet/blob/main/game.py)

### Step 3 — Train and predict

Train the chosen classifier on all seasons except the last (held-out test
set). Report:

- **Accuracy** on the test season vs. the `HomeTeamWins` baseline
  (always predicting home win).
- **Per-match probabilities** for H / D / A.
- **Confusion matrix** breakdown.

Hyperparameters live in JSON config files under `configs/` — e.g.
[`RandomForestClassifier.json`](https://github.com/clemsage/SportsBet/blob/main/configs/RandomForestClassifier.json).

### Step 4 — Simulate betting

If the user wants a bankroll simulation:

1. For each test-set match, place a flat-stake bet on the predicted
   outcome (or filter to value bets only).
2. Track bankroll evolution across the season.
3. Compare the ML predictor against the `HomeTeamWins` heuristic baseline.
4. Report final bankroll, total staked, ROI, and maximum drawdown.

Source: [`betting.py`](https://github.com/clemsage/SportsBet/blob/main/betting.py)

### Step 5 — Interpret and advise

- Explain which features drive the model (feature importances for tree
  models, coefficients for logistic regression).
- Note that typical accuracy is 45–55%; beating the bookmaker margin
  long-term is extremely difficult.
- Caveat: this is a simulation tool, not financial advice.

## Output Contract

Return a structured prediction and simulation summary:

```
League: {country} Division {division}
Seasons: {start} – {end}
Model: {classifier}
Test-season accuracy: {acc}%
Baseline accuracy (HomeTeamWins): {baseline_acc}%

Top predictions:
  {home_team} vs {away_team}: H {h}% | D {d}% | A {a}%
  ...

Betting simulation ({platform}):
  Initial bankroll: {initial}
  Final bankroll: {final}
  Total staked: {staked}
  ROI: {roi}%
  Value betting: {on/off}
```

## Limitations

- Data depends on football-data.co.uk availability; some seasons or
  divisions may have missing odds columns.
- Model accuracy rarely exceeds 55%; draws are the hardest class.
- Betting simulation assumes no slippage, no odds movement, and
  unlimited market liquidity — real-world results will differ.
- Covers five European countries only; other leagues need compatible
  CSV data in the same format.
