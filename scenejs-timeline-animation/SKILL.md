---
name: scenejs-timeline-animation
description: |
  Build choreographed CSS and JavaScript timeline animations using Scene.js.
  Generates ready-to-run HTML pages with keyframed sequences, easing curves,
  media sync, and multi-element orchestration via the Scene.js API.
---

# Scene.js Timeline Animation Builder

Create polished, timeline-based CSS/JS animations using the
[Scene.js](https://github.com/daybrush/scenejs) library (2 700+ stars).

**Provenance**

- Source repository: <https://github.com/daybrush/scenejs>
- Discovery article: <https://medium.com/@daybrush/introducing-scene-js-library-to-create-timeline-based-animation-fcb955458c35>
- Author: Daybrush (NAVER front-end engineer)
- TypeScript, MIT license, React/Vue/Svelte bindings available

## What This Skill Does

Given a storyboard, animation brief, or reference sketch, this skill generates
a self-contained HTML page with Scene.js-powered timeline animation. Each
output includes:

1. **Scene definition** -- keyframed property maps per CSS selector with
   timestamps in seconds, covering `transform`, `opacity`, `filter`, `clip-path`,
   colors, and custom CSS properties.
2. **Easing and timing** -- per-property easing (`ease-in-out`, cubic-bezier,
   steps) and global playback options (`iterationCount`, `direction`, `delay`,
   `playSpeed`).
3. **Multi-element orchestration** -- a `Scene` instance that choreographs
   multiple `SceneItem` tracks so elements enter, animate, and exit in
   coordinated sequence.
4. **Media integration guidance** -- when the brief includes audio or video
   cues, wiring instructions using `@scenejs/media` for synchronized playback.
5. **CSS structure** -- minimal reset, layout grid, and element styling so the
   animation renders correctly in modern browsers without external dependencies.

## Inputs

| Input | Required | Description |
|---|---|---|
| Animation brief | Yes | Free-text description of the desired animation: elements, motion path, timing, mood, and target viewport. |
| Storyboard / sketch | No | An image or diagram showing keyframes, element positions, or transition flow. |
| Color palette | No | Hex or named colors. Defaults to a neutral palette if omitted. |
| Duration | No | Total animation length in seconds. Defaults to 3 s. |
| Framework | No | `vanilla` (default), `react`, `vue`, or `svelte`. Selects the appropriate Scene.js binding package. |

## Workflow

### Step 1 -- Understand the brief

Read the user's animation description and any uploaded storyboard. Identify:

- **Elements**: what DOM nodes participate (text headings, cards, icons,
  images, SVG paths, decorative shapes).
- **Motion vocabulary**: slide, fade, scale, rotate, morph, bounce, wave,
  stagger, parallax, burst.
- **Timing intent**: simultaneous entrance, sequential cascade, overlapping
  waves, looping idle.
- **Viewport**: desktop-first, mobile-first, or responsive.

### Step 2 -- Design the timeline

Map each element to a `SceneItem` track. Define keyframes as time-indexed
property objects:

```javascript
import Scene from "scenejs";

const scene = new Scene({
  ".hero-title": {
    0:   { opacity: 0, transform: "translateY(40px)" },
    0.6: { opacity: 1, transform: "translateY(0px)" },
  },
  ".hero-subtitle": {
    0.3: { opacity: 0, transform: "translateY(20px)" },
    0.9: { opacity: 1, transform: "translateY(0px)" },
  },
}, {
  selector: true,
  easing: "ease-in-out",
  iterationCount: 1,
});
```

Choose easing per track. Stagger overlapping items by offsetting start times
rather than using `delay` on a single item. For looping ambient effects, set
`iterationCount: "infinite"` and `direction: "alternate"`.

### Step 3 -- Generate the HTML page

Produce a single `index.html` that:

- Loads Scene.js via CDN (`https://daybrush.com/scenejs/release/latest/dist/scene.min.js`)
  or via inline bundled script when offline use is needed.
- Contains all CSS inline in a `<style>` block.
- Contains the Scene definition in a `<script>` block that calls `.play()` on
  DOMContentLoaded.
- Includes the required DOM elements with class names matching the selectors.
- Uses viewport-relative units and flexbox/grid so the animation adapts to
  different screen sizes.

### Step 4 -- Add effects and polish

When the brief calls for advanced effects:

- **Kinetic typography**: use `@scenejs/effects` for typewriter, waving text,
  or character-stagger effects.
- **Audio/video sync**: use `@scenejs/media` to add `MediaScene` tracks that
  play or seek media in lockstep with the CSS timeline.
- **SVG path animation**: animate `stroke-dashoffset` and `stroke-dasharray`
  as Scene.js properties to draw SVG paths.
- **3D transforms**: combine `perspective`, `rotateX/Y/Z`, and
  `transform-origin` in keyframes for card flips, cube rotations, or parallax
  depth layers.

### Step 5 -- Validate and deliver

- Open the page in a browser (or mentally trace the timeline) to confirm
  elements animate as described.
- Ensure no external asset dependencies unless the user provided them.
- If the user requested a framework binding (React, Vue, Svelte), wrap the
  scene in the corresponding component (`<SceneComponent>`,
  `<vue-scene>`, `<SceneComponent>` for Svelte) with props for play
  control.

## Output

A single self-contained HTML file (or framework component file) with the
complete animation ready to run. Include a brief comment block at the top
summarizing the timeline structure and playback controls.
