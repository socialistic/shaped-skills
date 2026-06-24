---
name: animbase-declarative-animation
description: |
  Generate HTML pages with rich CSS animations using AnimBase's declarative
  data-attribute engine. Scroll-triggered fades, timed hero sequences, 3D card
  flips, coverflow galleries, color transitions, and complex transform chains
  are all authored via data-anim-init / data-anim-config attributes with
  per-value easing -- no custom JavaScript required.
---

# AnimBase Declarative Animation Generator

Build animated HTML pages using AnimBase -- the declarative animation engine
that drives CSS transitions through `data-*` attributes. The user describes
the visual effect they want; this skill produces a standalone HTML file with
the AnimBase CDN script and the correct attribute markup.

- Upstream library: https://github.com/agusmade/animbase
- Author article: https://medium.com/@krisnaparta/create-web-animations-without-writing-javascript-meet-animbase-b11d12630b58
- License: MIT
- npm: `animbase@1.2.0`

## When to use this skill

Use when the user wants to:

- create scroll-triggered animations (fade-in, slide-up, parallax) with no JS
- build a timed hero animation or looping intro sequence
- animate CSS transforms, colors, box-shadows, filters, or outlines across a
  frame-based timeline
- attach animation progress to an external controller (range slider, video
  `timeupdate`, scroll position)
- prototype interaction motion purely in HTML + CSS data attributes
- reproduce demos like coverflow, book-flip, 3D card, or kinetic typography
  using AnimBase

Do not use for: canvas/WebGL animation, Lottie/Bodymovin, SVG path morphing,
or physics simulations.

## Required inputs

Ask for whichever are missing:

1. **Target effect** -- what the user wants to animate (e.g. "fade-in cards on
   scroll", "looping color pulse on the hero heading").
2. **Animation mode** -- scroll-controlled, input-controlled, or timed
   (auto-start / triggered). Default to scroll if not specified.
3. **Elements to animate** -- how many, what kind (headings, cards, images,
   divs).
4. **Easing preference** (optional) -- e.g. `outBounce`, `spring`, `inOutQuad`.
   Defaults to `out`.
5. **Duration / frame range** (optional) -- e.g. 300 frames, 1000 frames.

## AnimBase API reference

### Include script (CDN)

```html
<script src="https://cdn.jsdelivr.net/npm/animbase@1.2.0/dist/animbase.iife.min.js"></script>
```

### Core attributes

| Attribute | Purpose |
|---|---|
| `data-anim-init` | JSON initial styles, e.g. `'{"opacity":"0","transform":"translateY(40px)"}' ` |
| `data-anim-config` | JSON keyframe timeline, keys are frame numbers, e.g. `'{"300":{"opacity":"1.out","transform":"translateY(0px.outBounce)"}}'` |
| `data-anim-strict` | `"true"` to throw on invalid JSON/easing (dev mode) |

### External-controlled mode (scroll, range, etc.)

| Attribute | Purpose |
|---|---|
| `data-anim-controlled-by` | Property to read: `scrollY`, `scrollTop`, `scrollLeft`, `value` |
| `data-anim-controller-ref` | CSS selector of controller element (default `window`) |
| `data-anim-listen` | Event to listen: `scroll`, `input`, `timeupdate`, etc. |

Defaults: if only `data-anim-controller-ref` is set, property = `value`,
event = `input`. If only `data-anim-controlled-by` is set, target = `window`,
uses scroll events.

### Timed / triggered mode

Place a config element (non-animated) per group:

```html
<div
  data-anim-trigger-group="hero"
  data-anim-trigger-config='{"speed":60,"once":false,"reverse":false,"autostart":true}'
></div>
```

Animated elements join the group via `data-anim-trigger-group="hero"`.

JS control: `AnimBase.trigger('hero')`, `AnimBase.pause('hero')`,
`AnimBase.resume('hero')`, `AnimBase.stop('hero')`, `AnimBase.seek('hero', frame)`.

### Per-value easing

Append `.easingName` to any numeric or color sub-value:

- Numbers: `translateY(40px.outBounce)`, `scale(0.5.spring)`
- Colors: `#5bf.outQuad`, `#fa2.inOut`

Available easings: `linear`, `ease`, `in`, `out`, `inOut`, `inQuad`,
`outQuad`, `inOutQuad`, `inCubic`, `outCubic`, `inOutCubic`, `inQuart`,
`outQuart`, `inOutQuart`, `inQuint`, `outQuint`, `inOutQuint`, `inSine`,
`outSine`, `inOutSine`, `inExpo`, `outExpo`, `inOutExpo`, `inCirc`,
`outCirc`, `inOutCirc`, `inBack`, `outBack`, `inOutBack`, `inBounce`,
`outBounce`, `inOutBounce`, `inElastic`, `outElastic`, `inOutElastic`,
`spring`.

### Hooks

`onStart` and `onFinish` callbacks can be set via the JS API on triggered
groups.

## Output format

Produce a single self-contained HTML file that:

1. Includes the AnimBase CDN script.
2. Contains inline CSS for layout/styling.
3. Uses only `data-anim-*` attributes for all animation -- no custom JS
   animation code.
4. Is responsive (works on mobile viewports).
5. Includes brief inline comments explaining each animated element's timeline.

## Quality checks

Before delivering, verify:

- All `data-anim-init` and `data-anim-config` values are valid JSON strings.
- Frame numbers in `data-anim-config` are positive integers (as string keys).
- Easing suffixes match the supported list above.
- Scroll-controlled elements have sensible frame ranges relative to viewport
  scroll distance.
- Timed groups have exactly one config element per group name.
