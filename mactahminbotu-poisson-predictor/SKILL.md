---
name: mactahminbotu-poisson-predictor
description: |
  Predict soccer match scores and 1X2 outcomes using Poisson regression
  on league fixture data. Fits a GLM from historical home/away goals,
  simulates the scoreline probability matrix, and returns the most
  likely result with win/draw/loss classification.
---

# Soccer Match Poisson Predictor

Predict soccer match scorelines and 1X2 outcomes using the Poisson
regression methodology from
[MacTahminBotu](https://github.com/umitkaanusta/MacTahminBotu), a
Turkish-language Telegram prediction bot with 22+ stars.

## Provenance

- **Source repo**: https://github.com/umitkaanusta/MacTahminBotu
- **Skill URL**: https://github.com/umitkaanusta/MacTahminBotu
- **License**: Not specified (educational project)
- **Original form**: Python Telegram bot delivering Turkish Super League
  predictions via Poisson regression. No prior SKILL.md existed. This
  skill distills the statistical prediction workflow into a conversational
  format applicable to any league.

## User Job

Given a league, a home team, and an away team, produce a Poisson
regression-based match prediction:

1. **Data preparation** — parse historical fixture results into
   home/away goal columns. Reshape into a stacked scoring/conceding
   format with a binary home-advantage indicator.
2. **Poisson GLM fitting** — fit a Generalized Linear Model:
   `goals ~ home_status + scoring + conceding` using the Poisson
   family. The `scoring` coefficient captures each team's attacking
   strength; `conceding` captures defensive weakness.
3. **Match simulation** — for the given home/away pair, predict
   expected goals for each side. Build an (N+1) x (N+1) probability
   matrix using the Poisson PMF over 0..max_goals for each team.
4. **Scoreline extraction** — find the most likely scoreline from the
   probability matrix. Derive 1X2 outcome (Home win / Draw / Away win).
5. **Summary statistics** — report home win / draw / away win aggregate
   probabilities by summing the appropriate regions of the matrix, plus
   Over/Under 2.5 goals probability.

## Inputs

| Input | Required | Description |
|---|---|---|
| League or competition | Yes | E.g. "Turkish Super Lig", "Premier League 2024-25" |
| Home team | Yes | Full club name |
| Away team | Yes | Full club name |
| Historical results CSV | No | CSV with HomeTeam, AwayTeam, HomeGoals (or FTHG), AwayGoals (or FTAG) columns. If omitted, reason from general knowledge of recent form. |
| Max goals ceiling | No | Upper bound for the Poisson matrix (default 10). |

## Methodology Reference

The analysis follows MacTahminBotu's Poisson pipeline:

- **Data source**: league fixture CSVs (originally fixturedownload.com
  format with Home Team, Away Team, Result columns).
- **Reshaping**: stack home and away records into a unified frame with
  `scoring` (team on attack), `conceding` (team on defense),
  `home_status` (1 if home, 0 if away), and `goals`.
- **Model**: `statsmodels` GLM with Poisson family and log link.
- **Simulation**: for each team, predict lambda (expected goals) given
  the opponent, then evaluate `poisson.pmf(k, lambda)` for k in
  0..max_goals. Outer product yields the full scoreline matrix.
- **Prediction**: argmax of the matrix gives the most probable exact
  score. Row/column sums give marginal win/draw/loss probabilities.

Key source files:
- Prediction engine: https://github.com/umitkaanusta/MacTahminBotu/blob/master/predict.py
- Telegram bot interface: https://github.com/umitkaanusta/MacTahminBotu/blob/master/bot.py

## Output Contract

Return a structured prediction:

```
Match: {home_team} vs {away_team}
League: {league}

## Poisson Model Parameters
- Home advantage coefficient: {value}
- {home_team} attack strength: {value}
- {away_team} attack strength: {value}

## Expected Goals
- {home_team} (home): {lambda_home:.2f}
- {away_team} (away): {lambda_away:.2f}

## Most Likely Scoreline
- {home_goals} - {away_goals} (probability: {p:.1%})

## 1X2 Prediction
- Home Win (1): {p_home:.1%}
- Draw (X): {p_draw:.1%}
- Away Win (2): {p_away:.1%}
- Prediction: {1 | X | 2}

## Over/Under 2.5
- Over 2.5: {p_over:.1%}
- Under 2.5: {p_under:.1%}
```

## Constraints

- State confidence honestly. Without historical CSV data, predictions are
  qualitative estimates informed by the Poisson framework, not fitted
  model outputs. Say so clearly.
- Never present predictions as guaranteed outcomes. This is for
  educational and entertainment purposes only.
- Cite MacTahminBotu's Poisson methodology when explaining the approach.
- If the user provides a CSV, validate columns before fitting.
- Support any football league, not just the Turkish Super Lig.
