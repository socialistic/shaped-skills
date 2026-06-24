---
name: ldrs-loader-generator
description: |
  Generate and customize lightweight loading spinners and animations using the
  ldrs library (44 types). Outputs ready-to-use HTML/CSS/SVG loader code or
  framework integration snippets for React, Vue, Svelte, and vanilla JS web
  components. Covers loader selection, theming, speed/size/color tuning, and
  standalone HTML extraction.
---

# ldrs Loader Generator

Help the user pick, configure, and integrate lightweight CSS/SVG loading
animations from the **ldrs** library by Griffin Johnston.

- Upstream: https://github.com/GriffinJohnston/ldrs
- Gallery: https://uiball.com/ldrs
- License: MIT
- Provenance: discovered via https://www.douyin.com/video/7629583823235304719

## When to use

Route here when the user wants to:

- add a loading spinner or progress indicator to a web page or app
- browse and pick from 44 minimalist loader/spinner designs
- get standalone HTML + CSS source for a loader without installing npm packages
- integrate ldrs web components or React components into an existing project
- customize loader appearance: size, color, stroke width, speed, background opacity
- compare loader types (dot-based, ring-based, orbit, bar, SVG path) for a specific UI context

Do not route here for: complex multi-step progress bars with percentage, skeleton screens, Lottie animations, or non-loading motion effects.

## Available loaders (44 types)

Ring family: `ring`, `ring2`, `tailspin`, `lineSpinner`, `squircle`, `square`
Dot family: `dotSpinner`, `dotPulse`, `dotStream`, `dotWave`, `bouncy`, `bouncyArc`
Orbit family: `orbit`, `chaoticOrbit`, `superballs`, `trio`
Bar family: `waveform`, `lineWobble`, `treadmill`, `zoomies`
SVG path: `trefoil`, `helix`, `spiral`, `reuleaux`, `infinity`, `cardio`
Physics: `newtonsCradle`, `momentum`, `metronome`, `leapfrog`, `jelly`, `jellyTriangle`
Misc: `grid`, `hatch`, `hourglass`, `mirage`, `miyagi`, `ping`, `pinwheel`, `pulsar`, `quantum`, `ripples`, `tailChase`, `wobble`, `bouncy`

## Required inputs

Ask for whichever are missing:

1. **Use case context** — where will the loader appear? (full-page overlay, inline button, skeleton placeholder, dark/light theme)
2. **Framework** — React, Vue, Svelte, Solid, Astro, Nuxt, or plain HTML/CSS
3. **Style preference** — minimal ring, playful dots, mechanical, organic, or let the skill recommend based on context
4. **Brand constraints** — primary color, size range, speed preference (subtle vs energetic)

## Workflow

### 1. Recommend loaders

Based on the user's context, suggest 2-3 loaders from the catalogue with reasoning. For each, show a preview description and the default attributes.

### 2. Generate integration code

**For npm/framework users:**

```bash
npm install ldrs
```

React example (v1.1.3+):
```jsx
import { Ring } from 'ldrs/react'
import 'ldrs/react/Ring.css'

<Ring size={40} color="#4f46e5" speed={1.8} stroke={5} bgOpacity={0.1} />
```

Web component (auto-defining):
```html
<script type="module">
  import 'https://cdn.jsdelivr.net/npm/ldrs@1/dist/auto/{loaderName}.js'
</script>
<l-{loader-name} size="40" color="#4f46e5" speed="1.8"></l-{loader-name}>
```

Web component (manual register):
```js
import { ring } from 'ldrs'
ring.register()
// or ring.register('my-custom-name')
```

**For standalone HTML/CSS (no npm):**

Extract the loader's SVG markup and CSS keyframe animation into a self-contained HTML snippet. Each loader uses CSS custom properties:
- `--uib-size` (px)
- `--uib-color` (any CSS color)
- `--uib-speed` (seconds)
- `--uib-bg-opacity` (0-1, where supported)

Provide the complete `<style>` + `<div>`/`<svg>` block the user can paste directly.

### 3. Customize

Adjust attributes to match brand. Common customizations:
- **size**: integer px, controls the largest dimension
- **color**: any CSS color value including `var(--custom-prop)`
- **speed**: seconds for one animation cycle (lower = faster)
- **stroke**: stroke width in px (ring/line loaders)
- **bg-opacity**: background track opacity 0-1 (ring loaders)

### 4. SSR considerations

Web components require client-side JS. For SSR frameworks:
- **Nuxt**: use `<ClientOnly>` wrapper or a client-only plugin
- **Astro**: use `client:only="react"` directive or `<script>` in an island
- **Next.js**: use `'use client'` directive or dynamic import with `ssr: false`

## Output contract

Deliver:
1. Recommended loader name(s) with rationale
2. Copy-paste-ready code snippet for the user's framework
3. Standalone HTML/CSS version if requested or if the user has no build tool
4. Customization notes for their specific brand colors/sizes

## Common attribute defaults

| Loader | size | color | speed | stroke | bg-opacity |
|--------|------|-------|-------|--------|------------|
| ring | 40 | black | 2 | 5 | 0 |
| dotSpinner | 40 | black | 0.9 | — | — |
| helix | 45 | black | 2.5 | — | — |
| trefoil | 40 | black | 1.4 | — | — |
| orbit | 35 | black | 1.5 | — | — |
| waveform | 35 | black | 1 | 3.5 | — |
| lineWobble | 80 | black | 1.75 | 5 | — |
