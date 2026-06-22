---
name: smartbets-soccer-predictor
description: >
  Predict soccer match outcomes using smartbetsAPI's non-ML probability engine.
  Takes home and away team names, scrapes live league data, and returns
  goal-average, both-teams-to-score %, over/under probabilities (1.5/2.5/3.5),
  1X2 result prediction, and a best-pick recommendation. Covers worldwide
  leagues via live web data. Built on Simatwa/smartbetsAPI.
requires:
  - Python 3.9+
  - "pip install smartbetsAPI"
---

# smartbets Soccer Predictor

> Provenance: shaped from
> [Simatwa/smartbetsAPI](https://github.com/Simatwa/smartbetsAPI)
> (Python, GPL-3.0, 18 stars). The original repo is a pip-installable
> prediction package with a FastAPI REST server and Python library API.
> It has no SKILL.md; this skill wraps its prediction pipeline into one
> coherent agent workflow.

## When to use

Use this skill when the user needs:

- **Single match prediction** -- provide two team names (home vs away),
  get back full probability breakdown: 1X2 result, goal-average, GG/NG,
  over/under 1.5 / 2.5 / 3.5 goals, and a best-pick recommendation.
- **Batch fixture predictions** -- multiple matchups in one go, each
  returning the same probability output.
- **Betting market analysis** -- compare the model's implied probabilities
  against bookmaker odds to find value.

The engine works across worldwide leagues (Premier League, La Liga,
Bundesliga, Serie A, Ligue 1, MLS, and many more) by scraping live
standings and recent form from public sources.

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| Home team name | Yes | e.g. "Manchester City", "Real Madrid" |
| Away team name | Yes | e.g. "Liverpool", "Barcelona" |
| Include position | No | Factor in league table position (default: off) |

No API keys required. Data is fetched automatically from public sources.

## Output contract

For each matchup, return a structured prediction:

```
Home: Manchester City  vs  Away: Liverpool

Goal average:      8.0
Both teams score:  65.0%
Over 1.5 goals:    60.0%
Over 2.5 goals:    45.0%
Over 3.5 goals:    30.0%
Win probability:   56.2%
Result:            1  (Home win)
Best pick:         GG  (Both teams to score)
```

Output fields:
- `g` -- goal average across recent fixtures
- `gg` -- probability (%) both teams score
- `ov15` / `ov25` / `ov35` -- over 1.5 / 2.5 / 3.5 goals probability (%)
- `choice` -- result occurrence probability (%)
- `result` -- predicted outcome: `1` (home), `1x` (home/draw), `x` (draw), `2x` (away/draw), `2` (away)
- `pick` -- best recommended betting pick

## Workflow

1. **Parse the user's request** -- extract home team and away team names.
   If the user provides a fixture list, process each pair sequentially.

2. **Install the package** if not already present:
   ```bash
   pip install smartbetsAPI
   ```

3. **Run the prediction** using the Python library:
   ```python
   from smartbets_API.predictor import predictor

   predict = predictor()
   result = predict.predictorD({1: "HomeTeam", 2: "AwayTeam"})
   print(result)
   ```

   Or using list format:
   ```python
   result = predict.predictorL(["HomeTeam", "AwayTeam"])
   ```

4. **Format and present** the results clearly, explaining each metric.
   When the user asks about value bets, compare `choice` probability
   against any bookmaker odds they provide.

5. **Handle errors** -- if a team name is not found, suggest similar names
   or ask the user to check spelling. The package searches online
   standings, so team names must match their league registration.

## Key source paths

- Library entry: [`smartbets_API/predictor.py`](https://github.com/Simatwa/smartbetsAPI/blob/main/smartbets_API/predictor.py)
- Bet analysis engine: [`smartbets_API/bet_analyzer.py`](https://github.com/Simatwa/smartbetsAPI/blob/main/smartbets_API/bet_analyzer.py)
- REST API server: [`smartbets_API/interface.py`](https://github.com/Simatwa/smartbetsAPI/blob/main/smartbets_API/interface.py)
- Usage examples: [`examples/`](https://github.com/Simatwa/smartbetsAPI/tree/main/examples)
