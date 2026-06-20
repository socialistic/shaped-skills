---
name: horoscopefree-daily-horoscope
description: >
  Fetch daily horoscope readings for any zodiac sign in English, Portuguese,
  or Spanish using the HoroscopeFree multilingual API. Returns the day's
  horoscope text with source attribution. Supports today, yesterday, and
  historical date lookups (English full archive, Spanish ~9 days, Portuguese
  today only). Cached per sign/date/language triple.
---

# HoroscopeFree Daily Horoscope

Retrieve up-to-date daily horoscope text for any of the 12 zodiac signs
in English, Portuguese, or Spanish. Uses the
[horoscopefree](https://github.com/vitorebatista/horoscopefree) TypeScript
library and REST API (6 stars, actively maintained) which scrapes validated
upstream horoscope sources per language and caches results to disk.

**Provenance**: public tool at
<https://github.com/vitorebatista/horoscopefree>.
Discovery source: <https://x.com/vitorebatista>.
This skill was shaped from that source; the original repository does not
contain a SKILL.md.

## When to use

- User wants today's horoscope for their zodiac sign
- User wants a horoscope in Portuguese or Spanish (not just English)
- User wants to compare horoscopes across languages for the same sign/date
- User wants yesterday's or a past date's horoscope reading
- User is building an app that needs a horoscope data source

## API endpoint

The public API is hosted at `https://horoscopefree.fly.dev`. No API key
required.

### Routes

| Method | Path | Description |
|---|---|---|
| `GET` | `/horoscope/:language/:sign/today` | Today's horoscope |
| `GET` | `/horoscope/:language/:sign/yesterday` | Yesterday's horoscope |
| `GET` | `/horoscope/:language/:sign/:date` | Specific date (YYYY-MM-DD) |
| `GET` | `/horoscope/:language/:sign` | Defaults to today |
| `GET` | `/health` | Liveness check |

### Parameters

| Param | Values |
|---|---|
| `:language` | `en`, `pt`, `es` |
| `:sign` | `aries`, `taurus`, `gemini`, `cancer`, `leo`, `virgo`, `libra`, `scorpio`, `sagittarius`, `capricorn`, `aquarius`, `pisces` |
| `:date` | `YYYY-MM-DD`, `today`, `yesterday` |

### Date archive support by language

| Language | Source | Archive depth |
|---|---|---|
| English (`en`) | horoscope.com | Full historical archive |
| Portuguese (`pt`) | joaobidu.com.br | Today only (past dates fail unless cached) |
| Spanish (`es`) | 20minutos.es | ~9 most recent days |

## Inputs

| Input | Required | Description |
|---|---|---|
| Zodiac sign | Yes | One of the 12 standard Western zodiac signs |
| Language | No (default: `en`) | `en`, `pt`, or `es` |
| Date | No (default: today) | `today`, `yesterday`, or `YYYY-MM-DD` |

If the user provides a birth date instead of a sign, derive the zodiac sign
from standard date ranges.

## Workflow

### 1. Determine the zodiac sign

Extract the user's zodiac sign from their message. If they provide a birth
date, map it:

| Sign | Date range |
|---|---|
| Aries | Mar 21 -- Apr 19 |
| Taurus | Apr 20 -- May 20 |
| Gemini | May 21 -- Jun 20 |
| Cancer | Jun 21 -- Jul 22 |
| Leo | Jul 23 -- Aug 22 |
| Virgo | Aug 23 -- Sep 22 |
| Libra | Sep 23 -- Oct 22 |
| Scorpio | Oct 23 -- Nov 21 |
| Sagittarius | Nov 22 -- Dec 21 |
| Capricorn | Dec 22 -- Jan 19 |
| Aquarius | Jan 20 -- Feb 18 |
| Pisces | Feb 19 -- Mar 20 |

### 2. Choose language and date

Default to English and today unless the user specifies otherwise. Respect
archive depth constraints: warn if requesting a past Portuguese horoscope
or a Spanish horoscope older than ~9 days.

### 3. Fetch the horoscope

Call the HoroscopeFree API:

```bash
curl https://horoscopefree.fly.dev/horoscope/en/aries/today
```

Response shape:

```json
{
  "sign": "aries",
  "date": "2026-06-21",
  "language": "en",
  "text": "Whatever tasks or chores you may have to perform today...",
  "source": "https://www.horoscope.com/us/horoscopes/general/...",
  "cached": false
}
```

### 4. Present the reading

Format the horoscope text naturally for the user. Include:

- The zodiac sign and date
- The full horoscope text
- Source attribution (the `source` URL from the response)
- Language label if not English

If the user asked for multiple signs or languages, fetch each separately
and present them together.

### 5. Handle errors

| HTTP status | Meaning | Action |
|---|---|---|
| 400 | Invalid sign, bad date, or future date | Ask user to correct input |
| 404 | Unsupported language | Offer en/pt/es alternatives |
| 502 | Upstream scrape failure | Inform user the source is temporarily unavailable |

## Output

Present the horoscope reading in a friendly, readable format. Always
attribute the source. If the response has `"cached": true`, the reading
is from a previous fetch for the same sign/date/language combination.

## Limitations

- Only three languages supported (en, pt, es)
- Portuguese horoscopes are today-only from the live source
- Spanish archive covers only ~9 recent days
- Future dates always return an error
- Cold starts on the Fly.io deployment may take ~5 seconds
- Horoscope content comes from third-party publishers; no editorial control
