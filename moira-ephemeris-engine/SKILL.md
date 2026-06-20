---
name: moira-ephemeris-engine
description: |
  Compute natal charts, planetary positions, house cusps, aspects, dignities,
  predictive techniques, and eclipses using the Moira pure-Python ephemeris
  engine grounded in JPL DE441 kernels and IAU 2000A/2006 standards. A
  transparent, auditable Swiss Ephemeris alternative.
---

# Moira Ephemeris Engine

Generate precise astrological charts and ephemeris data using
[Moira](https://github.com/TheDaniel166/moira), a pure-Python computation
engine with a transparent calculation chain from JPL DE441 planetary kernels.

## Provenance

- **Source repository**: https://github.com/TheDaniel166/moira
- **Discovery URL**: https://www.reddit.com/r/astrology/comments/1gitm4o/technical_question_software_libraries_for/
- **Language**: Python 3.10+ with C++17 native extension (pybind11)
- **License**: MIT
- **Install**: `pip install moira-astro`

## Capabilities

Moira covers Western, Vedic, and Hellenistic astrology with astronomical
precision:

| Domain | Features |
|---|---|
| **Positional astronomy** | Planetary/stellar positions, light-time iteration, aberration, nutation, parallax, topocentric reduction |
| **Chart computation** | Natal charts, 22 house systems (Placidus, Koch, Whole Sign, Equal, ...), ASC/MC/IC/Desc |
| **Aspects & patterns** | 22 aspect types, applying/separating/stationary states, T-Square, Grand Trine, Yod, Kite, Grand Cross |
| **Dignities** | Domicile, exaltation, triplicity, Egyptian terms, face, hayz, Almuten |
| **Arabic parts** | 499 computed lots (Fortune, Spirit, ...) |
| **Predictive** | Secondary/tertiary progressions, primary directions, solar/lunar returns, profections, Firdaria, solar arc |
| **Vedic** | Vimshottari Dasha, Ashtakavarga, Shadbala, Jaimini, Vargas, Panchanga, Muhurta, Nakshatra |
| **Eclipses** | Solar/lunar eclipse detection, Saros classification, contact times, local circumstances |
| **Heliacal** | Rising/setting dates, acronychal events, elongation extremes |
| **Fixed stars** | 1,809 named stars with proper motion and parallax from Gaia DR3 |
| **Bodies** | Planets, classical asteroids, Centaurs, Trans-Neptunians, lunar nodes/apsides, Uranian hypotheticals |
| **Synastry** | Inter-chart aspects, composite charts, Davison midpoint, house overlays |
| **Astrocartography** | Planetary line mapping for geographic relocation analysis |

## Inputs

| Input | Required | Description |
|---|---|---|
| **Date and time** | Yes | Birth or event datetime with timezone (UTC preferred) |
| **Location** | For houses/local phenomena | Latitude and longitude in decimal degrees |
| **House system** | No (default: Placidus) | One of 22 supported systems |
| **Sidereal mode** | No (default: tropical) | Ayanamsa selection for sidereal/Vedic calculations |
| **Technique** | Yes | What to compute: natal chart, progressions, returns, synastry, eclipse search, dasha, etc. |

## Workflow

### 1. Parse the user's request

Identify:
- The astrological technique requested (natal chart, transit, progression,
  return, synastry, dasha, eclipse search, dignity analysis, etc.)
- Birth/event data: date, time, timezone, location
- House system preference (default Placidus for Western, Whole Sign for Vedic)
- Tropical vs. sidereal framework
- Whether multiple charts are needed (synastry, composite, comparison)

### 2. Set up Moira and compute

```python
from datetime import datetime, timezone
from moira import Moira, HouseSystem
from moira.spk_reader import set_kernel_path

m = Moira()

# Natal chart
chart = m.chart(datetime(1990, 3, 15, 14, 30, tzinfo=timezone.utc))
houses = m.houses(
    datetime(1990, 3, 15, 14, 30, tzinfo=timezone.utc),
    latitude=40.7128, longitude=-74.0060,
    system=HouseSystem.PLACIDUS,
)
```

Key API entry points by technique:

| Technique | API |
|---|---|
| Natal chart | `m.chart(dt)` — planetary longitudes, latitudes, distances |
| Houses | `m.houses(dt, lat, lon, system)` — ASC, MC, cusps 1-12 |
| Aspects | `moira.aspects` — aspect detection with orb and state |
| Patterns | `moira.patterns.find_all_patterns(chart)` — T-Square, Grand Trine, Yod |
| Dignities | `moira.classical` — domicile, exaltation, triplicity, terms, face |
| Lots | `moira.lots` — 499 Arabic parts |
| Progressions | `moira._facade_predictive` — secondary, tertiary, solar arc |
| Returns | `moira._facade_predictive` — solar/lunar returns |
| Dasha | `moira.dasha` — Vimshottari, alternate dasha systems |
| Eclipses | `moira._facade_astronomy` — eclipse detection, Saros, contacts |
| Heliacal | `moira.heliacal` — visibility events |
| Stars | `moira.stars` — 1,809 fixed stars from registry |
| Synastry | `moira._facade_relationships` — inter-chart aspects, composite |
| Astrocartography | `moira.astrocartography` — planetary lines by location |

### 3. Interpret and present

- Present planetary positions with sign, degree, minute, and house placement.
- Show aspects sorted by tightness of orb.
- Highlight major aspect patterns (T-Square, Grand Trine, Yod, Grand Cross).
- For Vedic requests: use sidereal positions, report Nakshatra, Dasha periods,
  Shadbala strength, and Varga charts.
- For predictive queries: show progressed positions, return charts, or transit
  hits with exact dates.
- For eclipses: report type, magnitude, Saros series, and contact times.
- All positions are auditable: every reduction stage (light-time, aberration,
  nutation, parallax) is inspectable.

### 4. Accuracy notes

- Geocentric planetary positions verified to 0.576 arcseconds against IAU
  ERFA/SOFA reference implementation.
- Cross-validated against Swiss Ephemeris for astrological chart outputs.
- JPL DE441 kernel covers years -13200 to +17191.

## Output

Return a structured chart interpretation including:

- Planetary positions (sign, degree, minute, house)
- House cusps with ASC, MC, IC, Desc
- Aspect table with orbs, states (applying/separating), and patterns
- Dignity table when relevant
- Technique-specific results (Dasha periods, progressed positions, return
  chart, eclipse data, etc.)
- Provenance note: computed via Moira engine (JPL DE441 + IAU 2000A/2006)
