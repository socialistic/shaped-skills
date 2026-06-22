---
name: soccer-xg-model
description: |
  Train and analyze expected goals (xG) models from StatsBomb, Opta, or
  Wyscout soccer event stream data. Compute per-shot xG values, evaluate
  model calibration, compare feature sets and ML pipelines (logistic
  regression, XGBoost, MLP), and visualize shot maps with xG overlays.
  Built on the soccer_xg Python package from KU Leuven's DTAI group.
---

# Soccer xG Model Builder

Build, train, and evaluate expected goals (xG) models for soccer using
the methodology and codebase from
[soccer_xg](https://github.com/ML-KULeuven/soccer_xg) (252+ stars),
a Python package by the DTAI research group at KU Leuven.

## Provenance

- **Source repo**: https://github.com/ML-KULeuven/soccer_xg
- **Skill URL**: https://github.com/ML-KULeuven/soccer_xg
- **License**: Apache 2.0
- **Original form**: pip-installable Python library with Jupyter notebook
  tutorials; no prior SKILL.md existed. This skill distills the xG modeling
  workflow into a conversational format.
- **Research citation**: Robberechts & Davis, "How data availability affects
  the ability to learn good xG models", MLSA Workshop 2020.

## User Job

Given soccer event stream data (or a match/league scenario), guide the user
through building and interpreting an xG model:

1. **Data loading** — Load SPADL-format event data from StatsBomb, Opta, or
   Wyscout providers using the `DataApi`. Explain required HDF5 file structure
   and how to convert raw event feeds via
   [socceraction](https://github.com/ML-KULeuven/socceraction).

2. **Feature engineering** — Select and compute shot features: location
   (distance, angle), body part, assist type, game state, preceding action
   sequence, pressure index, speed of play. Reference the exhaustive feature
   set from the package:
   - Feature computation: https://github.com/ML-KULeuven/soccer_xg/blob/master/soccer_xg/features.py
   - API for data loading: https://github.com/ML-KULeuven/soccer_xg/blob/master/soccer_xg/api.py

3. **Pipeline selection** — Choose among the pre-built pipelines or design
   a custom one:
   - `openplay_logreg_basic` / `openplay_logreg_advanced` — logistic regression
   - `openplay_xgboost_basic` / `openplay_xgboost_advanced` — gradient boosting
   - Custom pipelines via `XGModel` with user-defined feature sets and classifiers
   - Separate models for open play, penalties (`PenaltyXGModel`), and free kicks
     (`FreekickXGModel`)
   - Pipeline code: https://github.com/ML-KULeuven/soccer_xg/blob/master/soccer_xg/ml/pipeline.py

4. **Training and validation** — Train on selected league-seasons, validate
   on held-out data. Report calibration metrics (Brier score, log loss,
   calibration curves) and discrimination metrics (ROC AUC).
   - Calibration analysis: https://github.com/ML-KULeuven/soccer_xg/blob/master/soccer_xg/calibration.py
   - Metrics: https://github.com/ML-KULeuven/soccer_xg/blob/master/soccer_xg/metrics.py

5. **Prediction and visualization** — Estimate xG for specific games, generate
   shot maps with xG values, compare model outputs across pipelines.
   - Visualization: https://github.com/ML-KULeuven/soccer_xg/blob/master/soccer_xg/visualisation.py
   - Core xG model: https://github.com/ML-KULeuven/soccer_xg/blob/master/soccer_xg/xg.py

## Inputs

| Input | Required | Description |
|---|---|---|
| SPADL event data (.h5) | Yes | HDF5 file(s) with SPADL-format actions from StatsBomb, Opta, or Wyscout. Convert raw feeds with socceraction first. |
| Provider name | Yes | One of: `statsbomb_opensource`, `wyscout_opensource`, `opta`. |
| Training league-seasons | Yes | List of (league, season) tuples for training, e.g. `[("ESP", "1718"), ("GER", "1718")]`. |
| Validation league-seasons | No | Held-out league-seasons for evaluation. Default: next available season. |
| Pipeline choice | No | One of the four pre-built pipelines or "custom". Default: `openplay_xgboost_advanced`. |
| Game IDs for prediction | No | Specific game IDs to compute xG values for. |

## Output Contract

Return a structured xG analysis:

```
## Data Summary
- Provider: {provider}
- Leagues/seasons loaded: {list}
- Total shots: {count}
- Shot types: open play {n}, penalty {n}, free kick {n}

## Model Configuration
- Pipeline: {pipeline_name}
- Features: {feature_set_summary}
- Open-play model: {classifier}
- Penalty model: {PenaltyXGModel or custom}
- Free-kick model: {FreekickXGModel or custom}

## Training Results
- Training set: {leagues-seasons}, {n_shots} shots
- Validation set: {leagues-seasons}, {n_shots} shots
- Brier score: {value}
- Log loss: {value}
- ROC AUC: {value}
- Calibration: {well-calibrated | over/under-confident at high/low xG}

## Predictions (if game IDs provided)
- Game {id}: {home} vs {away}
  - Total xG: {home_xg} - {away_xg}
  - Key chances: {top 3 shots with xG > 0.15}

## Recommendations
- {model comparison insight or feature importance finding}
- {calibration improvement suggestion if needed}
```

## Constraints

- Always specify which event data provider and SPADL conversion step is needed.
  Raw event JSON cannot be fed directly to soccer_xg.
- Distinguish open-play, penalty, and free-kick models. A single pipeline does
  not cover all shot types.
- Report calibration honestly. A model with good AUC but poor calibration
  produces misleading xG values.
- Cite the soccer_xg package and the Robberechts & Davis (2020) paper when
  explaining methodology.
- If the user has no SPADL data, explain the conversion workflow using
  socceraction before attempting to train.
