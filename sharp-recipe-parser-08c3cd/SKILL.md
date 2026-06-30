---
name: Recipe Ingredient & Instruction Parser
description: >
  Parse recipe ingredient lines and cooking instructions into structured data
  with quantities, units, temperatures, and timings. Supports English and
  Portuguese with automatic unit conversions.
---

# Recipe Ingredient & Instruction Parser

Parse free-text recipe ingredient lines and cooking instructions into clean,
structured JSON. Built on [sharp-recipe-parser](https://github.com/jlucaspains/sharp-recipe-parser),
a lightweight tokenizer-based parser that avoids brittle regexes.

**Provenance:** This skill wraps the public npm package
[`@jlucaspains/sharp-recipe-parser`](https://github.com/jlucaspains/sharp-recipe-parser).
The original repository does not contain a SKILL.md; this skill was shaped to
expose its parsing capabilities as a conversational workflow.

## What This Skill Does

Given one or more recipe ingredient lines or cooking instructions in natural
language, this skill:

1. **Parses ingredients** into structured fields: quantity, unit, ingredient
   name, and optional extras (e.g. "finely chopped").
2. **Parses instructions** to extract cooking temperatures (F/C), timings, and
   duration in seconds.
3. **Converts units** automatically when requested -- metric to imperial and
   vice versa (g/kg/mg/lb/oz, ml/l/cup/tbsp/tsp, F/C).
4. **Supports multiple languages**: English (`en`, `en-us`) and Portuguese
   (`pt`, `pt-br`).

## Inputs

The user provides:

- **Ingredient lines** -- one or more free-text lines such as `300g flour`,
  `1 1/2 cups milk`, `2 colheres de sopa de azeite`.
- **Instruction lines** (optional) -- cooking steps such as `Bake at 400F for
  30 minutes` or `Simmer on low for 1 hour 15 minutes`.
- **Language** (optional) -- `en`, `en-us`, `pt`, or `pt-br`. Defaults to `en`.
- **Options** (optional):
  - Whether to include alternative unit conversions.
  - Whether to extract extras/modifiers from ingredient lines.
  - Whether to include alternative temperature units in instructions.

## Output Contract

Return a JSON object with the parsed results:

```json
{
  "ingredients": [
    {
      "original": "300g flour, very fine",
      "quantity": 300,
      "unit": "gram",
      "unitText": "g",
      "ingredient": "flour",
      "extra": "very fine",
      "minQuantity": 300,
      "maxQuantity": 300,
      "alternativeQuantities": [
        { "quantity": 0.6614, "unit": "lb", "unitText": "pound" },
        { "quantity": 10.5822, "unit": "oz", "unitText": "ounce" }
      ]
    }
  ],
  "instructions": [
    {
      "original": "Bake at 400F for 30 minutes.",
      "totalTimeInSeconds": 1800,
      "temperature": 400,
      "temperatureUnit": "fahrenheit",
      "alternativeTemperatures": [
        { "quantity": 204.4444, "unit": "C" }
      ]
    }
  ]
}
```

## Workflow

1. Receive the user's ingredient and/or instruction lines.
2. Detect or confirm the language. Default to `en` if ambiguous.
3. Parse each ingredient line: extract quantity (including fractions and ranges),
   unit, ingredient name, and extras.
4. Parse each instruction line: extract temperature, temperature unit, and time
   items with durations.
5. When the user requests unit conversions, include alternative quantities for
   ingredients and alternative temperature units for instructions.
6. Present the structured result as formatted JSON.
7. Offer to re-parse with different options (e.g. switch language, toggle unit
   conversions) if the user asks.

## Parsing Rules

These rules reflect how sharp-recipe-parser tokenizes and interprets input:

- **Numbers**: integers, decimals, fractions (`1/2`, `1 1/4`), word-form
  numbers (`one`, `two`), and ranges (`1-2`, `1 to 2`).
- **Units**: looked up from a language-specific dictionary after singularization.
  Supported units include g, kg, mg, lb, oz, ml, l, cup, tbsp, tsp, and more.
- **Ingredient name**: the remaining tokens after quantity and unit extraction.
- **Extras**: comma-separated modifiers after the ingredient name (e.g.
  `flour, sifted`), extracted only when `includeExtra` is enabled.
- **Temperature**: numbers followed by F/C/fahrenheit/celsius markers.
- **Time**: numbers followed by time-unit words (seconds, minutes, hours).
  Multiple time segments are summed into `totalTimeInSeconds`.

## Limitations

- Only English and Portuguese language dictionaries are built in. Other
  languages will fail unit and word-number lookups.
- The parser is line-oriented: each ingredient or instruction should be on its
  own line.
- Ambiguous inputs (e.g. "a pinch of salt") may not produce a numeric quantity.
