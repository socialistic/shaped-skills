---
name: immanuel-natal-chart
description: |
  Generate detailed natal charts, solar returns, progressions, composites,
  synastries, and transit reports from birth date and coordinates using
  the Immanuel Python package (Swiss Ephemeris). Returns human-readable
  and JSON-formatted planet positions, houses, aspects, dignities, and
  weightings.
---

# Immanuel Natal Chart Generator

Generate complete astrology chart data from birth details. Powered by the
Immanuel Python package which wraps the Swiss Ephemeris and aligns
calculations with astro.com and Astro Gold standards.

## Provenance

- Original repo: <https://github.com/theriftlab/immanuel-python>
- Discovery: <https://www.reddit.com/r/Python/comments/17z7i9k/package_for_astrology_data/>
- License: AGPL-3.0

The original repository is a Python library (pip install immanuel) with no
hosted demo or existing SKILL.md. This skill wraps its public workflow into
a socialistic.ai-compatible interface.

## Supported Chart Types

| Chart | Required input | Description |
|---|---|---|
| Natal | birth date + coordinates | Planet/point positions, houses, aspects, dignities, weightings |
| Solar Return | birth date + coordinates + target year | Chart for Sun's exact return to natal position in a given year |
| Progressed | birth date + coordinates + progression date | Secondary progressions with astro.com-compatible MC methods |
| Composite | two subjects' birth data | Midpoint composite chart for relationship analysis |
| Synastry | two subjects' birth data | Cross-aspects between two natal charts |
| Transits | coordinates (optional) | Current planetary positions and aspects |

## Inputs

| Input | Required | Description |
|---|---|---|
| Birth date and time | yes | ISO format (YYYY-MM-DD HH:MM) or natural language date |
| Birth location | yes | City name, coordinates (e.g. 32n43 117w09), or decimal degrees |
| Chart type | no | natal (default), solar_return, progressed, composite, synastry, transits |
| Target year | solar return only | Year for the solar return chart |
| Progression date | progressed only | Date to progress the chart to |
| Partner birth data | composite/synastry | Second person's date, time, and location |
| House system | no | Placidus (default), Whole Sign, Equal, Koch, Regiomontanus, etc. |
| Extra objects | no | Additional asteroids (Ceres, Pallas, Juno, Vesta, Chiron, etc.) |
| Locale | no | en_US (default), pt_BR, es_ES, de_DE |

## Workflow

1. **Parse birth data** -- extract date/time, latitude, longitude, and
   optional timezone from the user's input. Convert city names to
   coordinates when needed.

2. **Configure chart settings** -- apply requested house system, extra
   chart objects, aspect orb preferences, and locale. Use sensible
   defaults (Placidus houses, standard planets + Chiron, default orbs).

3. **Generate chart** -- produce the requested chart type with full data:
   - Planet and point positions with sign, degree, house, movement
   - Dignity scores (ruler, exalted, detriment, fall, peregrine, mutual
     reception)
   - All aspects with orb, applicative/separative, and associate status
   - Houses with cusps and sign placements
   - Weightings (element, modality, quadrant distribution)
   - Moon phase, diurnal/nocturnal status, chart shape type

4. **Format output** -- present a human-readable chart summary plus
   structured data. Highlight key placements (Sun, Moon, Ascendant),
   tight aspects, and notable dignity patterns.

5. **Interpret (optional)** -- when the user asks, provide brief
   astrological interpretation of major placements and aspect patterns.
   Clearly label interpretive content as such.

## Output Contract

Return a structured response containing:

```json
{
  "chart_type": "natal",
  "subject": {
    "date_time": "2000-01-01 10:00:00-08:00",
    "latitude": "32N43",
    "longitude": "117W09",
    "timezone": "America/Los_Angeles"
  },
  "summary": "Sun in Capricorn (11th), Moon in Scorpio (8th), Ascendant in Aries...",
  "objects": [
    {
      "name": "Sun",
      "sign": "Capricorn",
      "degree": "10:37:26",
      "house": 11,
      "movement": "Direct",
      "dignities": ["Triplicity Ruler by mutual reception"],
      "score": 3
    }
  ],
  "houses": [...],
  "aspects": [...],
  "weightings": {
    "elements": {"Fire": 3, "Earth": 4, "Air": 2, "Water": 3},
    "modalities": {"Cardinal": 4, "Fixed": 3, "Mutable": 5}
  },
  "moon_phase": "Third Quarter",
  "diurnal": true,
  "chart_shape": "Bowl"
}
```

## Constraints

- Coordinates and timezone must resolve unambiguously; ask the user to
  clarify if a city name is ambiguous.
- Do not fabricate chart data; all positions must derive from the Swiss
  Ephemeris calculations.
- Interpretive text is optional and must be clearly separated from
  computed chart data.
- Supported date range matches the Swiss Ephemeris coverage (approx.
  5400 BC to 5400 AD).
