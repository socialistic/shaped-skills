---
name: pyjhora-vedic-astrology
description: >
  Compute a full Vedic (Jyotish) horoscope from birth data using the PyJHora
  engine. Covers Rashi and Navamsa charts, 50+ Dasha systems (Vimshottari,
  Ashtottari, Yogini, Narayana, Chara, Kalachakra, etc.), Shadbala and
  Vimsopaka strength, Ashtakavarga, 284+ yogas, dosha detection (Kala Sarpa,
  Manglik, Pitru), divisional charts D2-D300, marriage compatibility (North
  and South Indian), Panchanga, eclipses, transits, and Tajaka annual charts.
  Based on PVR Narasimha Rao's "Vedic Astrology - An Integrated Approach."
---

# PyJHora Vedic Astrology Chart Reading

## Provenance

- Resolved importable skill: https://github.com/naturalstupid/PyJHora
- Discovery source: https://www.reddit.com/r/vedicastrologyexperts/comments/1pfopmi/i_wrote_a_python_engine_to_calculate_shadbala_d60/

This skill was shaped from the public PyJHora repository (190 stars,
actively maintained). The original repo does not contain a SKILL.md.
PyJHora implements nearly all techniques from PVR Narasimha Rao's book
and mirrors the calculations of Jagannatha Hora V8.0 software, with
6800+ verified test cases.

## When to use

- User provides birth date, time, and place and wants a Vedic natal chart
  reading (Rashi, Navamsa, or any divisional chart D2-D300)
- User asks about planetary Dasha periods (running Dasha, upcoming
  transitions, Bhukti/Antardasha timing)
- User wants Shadbala, Vimsopaka, or Ashtakavarga strength analysis
- User asks about specific yogas (Raja, Dhana, Gajakesari, Pancha
  Mahapurusha, etc.) or doshas (Kala Sarpa, Manglik, Pitru)
- User wants marriage compatibility scoring (Ashtakoota / Dashakoota)
- User asks for Panchanga (Tithi, Nakshatra, Yoga, Karana) for a date
- User wants eclipse predictions, transit analysis, or Tajaka annual charts
- User wants to compare multiple Dasha systems for the same chart

## Required environment

```
pip install PyJHora pyswisseph
```

Python 3.9+ required. Download Swiss Ephemeris data files from
https://github.com/naturalstupid/PyJHora/tree/main/src/jhora/data/ephe
and place them in the package's `data/ephe/` directory.

Multilingual support: English, Tamil, Telugu, Hindi, Kannada, Malayalam.

## Inputs

| Input | Required | Description |
|---|---|---|
| Birth date | Yes | Date of birth (year, month, day). Negative year for BCE dates. |
| Birth time | Yes | Time of birth (hours, minutes, seconds), 24-hour format. |
| Birth place | Yes | City and country code (e.g. "Chennai, IN", "New York, US"). Resolved to lat/lon/timezone automatically. |
| Ayanamsa | No | Ayanamsa mode (default: Lahiri). Options include Raman, KP, Yukteswar, etc. |
| Chart type | No | Specific divisional chart (D1-D300) or chart style (South Indian, North Indian, East Indian). |
| Dasha system | No | Which Dasha system to compute (default: Vimshottari). 50+ systems available. |

## Workflow

### 1. Collect birth data

Extract date, time, and place from the user's input. Parse flexibly:
accept "May 15, 1990 2:30 PM" or "1990-05-15 14:30" or similar natural
language. Resolve the place name to coordinates using PyJHora's built-in
place database (`jhora.place_db`).

```python
from jhora.panchanga.drik import Place
place = Place("Chennai, IN")  # auto lat/lon/tz
```

### 2. Compute the horoscope

Use `jhora.horoscope.main` or individual modules as needed:

```python
from jhora.horoscope.chart.charts import rasi_chart, divisional_chart
from jhora.horoscope.chart.charts import planets_positions_from_jd
from jhora.panchanga.drik import julian_day_at_birth

jd = julian_day_at_birth(year, month, day, hours, minutes, seconds, place)
planet_positions = planets_positions_from_jd(jd, place)
```

### 3. Generate requested analysis

Route to the appropriate modules based on what the user asks:

**Dasha periods** (50+ systems):
- Graha dashas: `jhora.horoscope.dhasa.graha.vimsottari`, `.ashtottari`,
  `.yogini`, `.kaala`, `.moola`, `.shodasottari`, `.dwisatpathi`, etc.
- Rashi dashas: `jhora.horoscope.dhasa.raasi.narayana`, `.chara`,
  `.kalachakra`, `.sudasa`, `.sthira`, `.trikona`, `.mandooka`, etc.
- Annual dashas: `jhora.horoscope.dhasa.annual.mudda`, `.patyayini`
- Sudarshana Chakra: `jhora.horoscope.dhasa.sudharsana_chakra`

**Strengths**: `jhora.horoscope.chart.strength` (Shadbala, Vimsopaka,
Ishta/Kashta Phala, Drig Bala, Cheshta Bala)

**Yogas**: `jhora.horoscope.chart.yoga` (284+ combinations including
Raja Yoga, Dhana Yoga, Pancha Mahapurusha, Gajakesari, Budha-Aditya,
Saraswati, Lakshmi, etc.)

**Doshas**: `jhora.horoscope.chart.dosha` (Kala Sarpa, Manglik/Kuja,
Pitru, Graha, Sarpa, etc.)

**Ashtakavarga**: `jhora.horoscope.chart.ashtakavarga` (Bhinnashtakavarga,
Sarvashtakavarga, Prastarashtakavarga, transit predictions)

**Divisional charts**: `jhora.horoscope.chart.charts` (Hora D2,
Drekkana D3, Chaturthamsa D4, Saptamsa D7, Navamsa D9, Dasamsa D10,
Dwadasamsa D12, Shodasamsa D16, Vimsamsa D20, Chaturvimsamsa D24,
Saptavimsamsa D27, Trimsamsa D30, Khavedamsa D40, Akshavedamsa D45,
Shashtiamsa D60, up to D300)

**Marriage compatibility**: `jhora.horoscope.match.compatibility`
(Ashtakoota for North Indian, Dashakoota for South Indian — Varna,
Vashya, Tara, Yoni, Graha Maitri, Gana, Bhakoota, Nadi scores)

**Panchanga**: `jhora.panchanga.drik` (Tithi, Nakshatra, Yoga, Karana,
Vaara, sunrise/sunset, moonrise/moonset)

**Eclipses**: `jhora.panchanga.eclipse` (solar and lunar eclipse
predictions with visibility)

**Transits**: `jhora.horoscope.transit.tajaka` and `.tajaka_yoga`
(Tajaka annual chart, Sahams, Muntha, transit aspects)

### 4. Format the reading

Present the analysis in clear sections. For a natal chart reading, cover:
1. Lagna (Ascendant) sign, degree, and nakshatra
2. Planet positions by sign, nakshatra, and nakshatra pada
3. Key yogas present in the chart
4. Active doshas (if any)
5. Current Vimshottari Dasha-Bhukti period and upcoming transitions
6. Strength highlights (strongest/weakest planets by Shadbala)

For compatibility, present the match scores and total points with
interpretation of each koota.

## Output contract

Return the computed Vedic astrology analysis in structured text with
clear section headings. Include:
- Planet positions table (sign, degree, nakshatra, pada, retrograde status)
- Relevant chart data for the requested analysis type
- Interpretive notes tied to classical Jyotish principles
- Source attribution to PyJHora and the Narasimha Rao methodology

## Source map

Key upstream paths in https://github.com/naturalstupid/PyJHora:

- `src/jhora/horoscope/chart/charts.py` — core chart and planet position computations
- `src/jhora/horoscope/chart/strength.py` — Shadbala, Vimsopaka, and related bala
- `src/jhora/horoscope/chart/yoga.py` — 284+ yoga detection
- `src/jhora/horoscope/chart/dosha.py` — dosha identification
- `src/jhora/horoscope/chart/ashtakavarga.py` — Ashtakavarga analysis
- `src/jhora/horoscope/dhasa/graha/vimsottari.py` — Vimshottari Dasha (most common system)
- `src/jhora/horoscope/dhasa/raasi/narayana.py` — Narayana Dasha (Jaimini)
- `src/jhora/horoscope/match/compatibility.py` — marriage compatibility scoring
- `src/jhora/panchanga/drik.py` — core astronomical calculations, Panchanga
- `src/jhora/panchanga/eclipse.py` — eclipse predictions
- `src/jhora/horoscope/transit/tajaka.py` — Tajaka annual chart and transits
- `src/jhora/horoscope/main.py` — orchestration entry point
