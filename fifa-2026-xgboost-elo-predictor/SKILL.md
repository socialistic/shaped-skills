---
name: fifa-2026-xgboost-elo-predictor
description: |
  Predict FIFA World Cup 2026 match outcomes using an XGBoost classifier with
  dynamic ELO ratings, team form tracking, goal statistics, and head-to-head
  analysis. Reproduces the end-to-end pipeline from data collection through
  feature engineering to match-level probability forecasts.
---

# FIFA 2026 XGBoost + ELO Match Predictor

Forecast FIFA World Cup 2026 match results by combining dynamic ELO ratings
with XGBoost classification over engineered team-level features.

**Source**: [rivu-intel45/FIFA-2026-Winner-Prediction](https://github.com/rivu-intel45/FIFA-2026-Winner-Prediction)

## Job

Given two national teams (or a set of WC 2026 fixtures), produce:

1. **Win / Draw / Loss probabilities** from the XGBoost classifier.
2. **Predicted match result** (home win, draw, or away win) with confidence.
3. **ELO-based team strength comparison** using dynamic ratings updated from
   historical international matches.
4. **Tournament simulation** when a full fixture list is provided — walk
   through group stages and project knockout seeding.

## Required Inputs

- **Match fixture** — at minimum two national team names (e.g. "Argentina vs
  Mexico"). Optionally a CSV of multiple fixtures with `home_team`,
  `away_team` columns.
- **Optional overrides** — custom ELO ratings, recent form notes, injury
  reports, or neutral-venue flag. The pipeline ships curated historical data
  for all qualified teams; overrides refine defaults.

## Modeling Pipeline

The source repository implements three sequential Jupyter notebook stages
under `FIFA Prediction/`:

| Stage | Notebook | Purpose |
|---|---|---|
| Data collection | `collect_data.ipynb` | Scrape/load historical international match results; compute dynamic ELO ratings for every team; identify 2026-qualified squads |
| Feature engineering | `feature_engineering.ipynb` | Build per-match feature vectors: ELO diff, ELO win probability, recent form (last N matches), avg goals scored/conceded, expected goal difference, H2H win rate, tournament weight, neutral venue flag |
| Model training | `model_training.ipynb` | Train XGBoost classifier (200 trees, depth 6, lr 0.1, subsample 0.8); evaluate accuracy + classification report; rank feature importance; predict all 2026 fixtures |

### Feature Set

- **ELO features**: home ELO, away ELO, ELO difference, ELO win probability
- **Form features**: home recent form, away recent form, form difference
- **Goal stats**: avg goals scored, avg goals conceded, expected goal
  difference (XGD)
- **H2H features**: home team historical win rate, historical draw rate
- **Context features**: tournament importance weight, neutral venue indicator,
  World Cup indicator

### Model Configuration

- 200 estimators, max depth 6, learning rate 0.1
- Column and row subsampling at 0.8
- Three-class output: 0 = away win, 1 = draw, 2 = home win

## Datasets

The pipeline produces and consumes eight CSVs under `FIFA Prediction/data/`:

| File | Content |
|---|---|
| `match_results.csv` | Historical international match records |
| `elo_ratings.csv` | Full ELO rating history per team per match |
| `current_elo.csv` | Latest ELO snapshot for each team |
| `2026_teams.csv` | 48 qualified WC 2026 squads |
| `features_train.csv` | Training feature matrix |
| `features_test.csv` | Test feature matrix |
| `features_2026.csv` | 2026 fixture feature matrix |
| `2026_predictions.csv` | Final match-level predictions |

## Output Contract

Return a structured prediction:

```json
{
  "fixtures": [
    {
      "home_team": "Argentina",
      "away_team": "Mexico",
      "win_prob": { "home": 0.684, "draw": 0.182, "away": 0.134 },
      "predicted_result": "home_win",
      "confidence": 0.684
    }
  ],
  "elo_snapshot": {
    "Argentina": 2048,
    "Mexico": 1886
  },
  "notes": "Probabilities from XGBoost 3-class classifier with ELO + form features"
}
```

## Constraints

- The model does not incorporate real-time lineup or injury data; supply these
  as optional overrides for better late-stage accuracy.
- Draw class is typically the hardest to predict accurately; consider the
  draw probability as a softer signal.
- ELO ratings reflect historical performance up to the training cutoff;
  very recent form shifts may not be captured unless overridden.

## Provenance

- **Skill URL**: https://github.com/rivu-intel45/FIFA-2026-Winner-Prediction
- **Source URL**: https://github.com/rivu-intel45/FIFA-2026-Winner-Prediction
- **License**: MIT
- **Original author**: Protyay Saha (rivu-intel45)
