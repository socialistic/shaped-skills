---
name: Kerykeion Astrology Chart Generator
description: |
  Generate publication-quality natal, synastry, transit, composite, and
  solar/lunar return charts using the Kerykeion Python astrology library.
  Compute planetary positions, house cusps, aspects, relationship scores,
  and moon phases via Swiss Ephemeris. Supports 47+ sidereal modes,
  multiple house systems, and 10 chart languages.
original_repo: g-battaglia/kerykeion
original_author: g-battaglia
original_url: https://github.com/g-battaglia/kerykeion
conformed_by: socialistic.ai
conformed_at: 2026-06-21
---

# Kerykeion Astrology Chart Generator

Source: https://github.com/g-battaglia/kerykeion
Discovered via: https://www.reddit.com/r/opensource/comments/1o9yr3v/launch_astrologer_highprecision_astrological/

Generate high-precision astrological charts and structured data using the
Kerykeion Python library (657 stars, AGPL-3.0, Swiss Ephemeris backend).
Designed for astrologers, developers building chart services, and anyone
who wants natal/synastry/transit analysis without a commercial API.

## What It Does

Given birth data (date, time, geographic coordinates), this skill:

1. Computes planetary positions, house cusps, and aspects using Swiss
   Ephemeris
2. Generates SVG charts in classic or modern style with four themes
   (classic, dark, light, black-and-white)
3. Supports seven chart types:
   - **Natal** -- single birth chart with planets, houses, aspects
   - **Synastry** -- two-person overlay for relationship comparison
   - **Transit** -- current sky against natal positions
   - **Composite** -- midpoint chart for a relationship entity
   - **Solar Return** -- annual return of Sun to natal position
   - **Lunar Return** -- monthly return of Moon to natal position
   - **Wheel-only** -- minimal outer-ring visualization
4. Extracts structured data: aspect tables, element/quality distribution,
   relationship compatibility scores, moon phase details
5. Serializes chart context to XML for downstream LLM consumption

## Inputs

- **Birth data** (required for each subject):
  - Name, birth year/month/day, birth hour/minute
  - Geographic coordinates (longitude, latitude) and timezone string
  - Example: "John Lennon, 1940-10-09 18:30, lng=-2.9833, lat=53.4,
    tz=Europe/London"
- **Chart type** (optional, default natal): natal, synastry, transit,
  composite, solar_return, lunar_return
- **Second subject** (required for synastry/composite): same birth data
  fields
- **Transit moment** (required for transit charts): date/time and location
- **Return target date** (required for solar/lunar return): year and month
  to search from
- **Zodiac type** (optional): Tropical (default) or Sidereal with mode
  (Lahiri, Fagan-Bradley, etc.)
- **House system** (optional, default Placidus): Morinus, Koch,
  Whole-Sign, Equal, Campanus, Regiomontanus, etc.
- **Perspective** (optional): Geocentric (default) or Heliocentric
- **Chart language** (optional): EN, FR, PT, ES, TR, RU, IT, CN, DE, HI
- **Style** (optional): classic or modern
- **Theme** (optional): classic, dark, light, black-and-white
- **Active points** (optional): include fixed stars like Sirius, Regulus,
  Aldebaran, Antares, Fomalhaut
- **Distribution method** (optional): pure_count or weighted with custom
  weights

## Output

Depending on the request:

- **SVG chart file** -- publication-quality vector graphic of the selected
  chart type, optionally minified or with CSS variables removed
- **Aspect grid SVG** -- standalone aspect table visualization
- **Structured data** -- JSON with planetary positions, house cusps,
  aspects (with orb, movement, difference), element/quality distributions
- **Relationship score** -- numeric compatibility score with description
- **Moon phase report** -- phase name, emoji, illumination percentage
- **Text report** -- formatted ASCII table of positions and aspects
- **AI context XML** -- serialized chart data optimized for LLM pipelines

## How to Use

1. Provide birth data for the subject(s):
   - "Generate a natal chart for someone born 1990-07-15 at 10:30 in
     Rome (41.9028 N, 12.4964 E)"
   - "Create a synastry chart comparing Person A (1988-03-20 14:00,
     New York) and Person B (1992-11-05 09:30, London)"
2. Specify chart type and preferences:
   - "Use sidereal Lahiri zodiac with Whole-Sign houses"
   - "Dark theme, modern style, include Sirius and Regulus"
3. For transit/return charts, provide the target moment:
   - "Show transits for today against my natal chart"
   - "Find the next solar return after October 2024"
4. Request specific data extractions:
   - "What aspects does this chart have? Show the aspect grid."
   - "Calculate relationship compatibility score"
   - "What moon phase was active at this birth time?"

### Example Prompts

- "Draw my natal chart: born 1995-12-25 at 08:15 in Tokyo (35.6762 N,
  139.6503 E), dark theme"
- "Compare compatibility: Alice born 1990-03-15 14:30 Rome vs Bob born
  1988-07-22 09:00 Rome -- show synastry chart and score"
- "Transit chart for today (2026-06-21 12:00 UTC, New York) against natal
  born 1985-04-10 06:00 Chicago"
- "Solar return chart for 2025 starting from October, Lahiri sidereal"
- "Moon phase details for 2025-04-01 07:51 London"
- "Export natal chart data as JSON with weighted element distribution
  (Sun weight 3.0)"

## Technical Notes

- Kerykeion uses Swiss Ephemeris for high-precision planetary calculations
- Offline mode (no external API calls) is default; GeoNames lookup
  available for city-to-coordinate resolution
- AGPL-3.0 license: applications importing the library directly must use
  compatible open-source licenses
- 23 fixed stars available as optional active points
- Chart languages: EN, FR, PT, ES, TR, RU, IT, CN, DE, HI
