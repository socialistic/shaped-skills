---
name: spritejs-canvas-animation
description: |
  Generate interactive Canvas graphics and animations using SpriteJS -- a
  cross-platform high-performance 2D/3D rendering framework with a DOM-like
  API. Sprite sheets, Web Animations API keyframes, vector shapes, text labels,
  event handling, and multi-layer composition are all expressed through a clean
  object model instead of raw canvas draw calls.
---

# SpriteJS Canvas Animation Generator

Build animated, interactive canvas scenes using SpriteJS -- the cross-platform
high-performance graphics framework that lets you manipulate canvas elements
like DOM nodes.

- Upstream library: https://github.com/spritejs/spritejs
- Original article: https://juejin.cn/post/6844903622560448526
- Author: 360 Qiwu Team (奇舞团)
- License: MIT
- CDN: `spritejs@3`
- Stars: 5.3K+

## When to use this skill

Use when the user wants to:

- create 2D canvas animations with sprites, shapes, or text without raw
  `CanvasRenderingContext2D` calls
- build sprite-sheet-driven character or object animation
- compose multi-layer canvas scenes (background + foreground + UI)
- animate properties (position, rotation, scale, opacity, color) with
  Web Animations API-style keyframes and easing
- add mouse/touch event handling to canvas elements (click, hover, drag)
- render vector shapes (Path, Polyline, Ellipse, Arc, Ring, Star, Rect,
  Triangle, Regular polygon) with fill, stroke, and animation
- create data visualization overlays using SpriteJS + D3
- prototype canvas interactions in a single HTML file

Do not use for: CSS-only animation, Lottie/Bodymovin, SVG-only morphing,
Three.js/WebGL-first 3D scenes, or React/Vue component animation libraries.

## Required inputs

Ask for whichever are missing:

1. **Scene description** -- what the user wants to see on the canvas (e.g.
   "a bouncing ball with a trail", "an animated dashboard gauge",
   "a sprite walking across a parallax background").
2. **Element types** -- which SpriteJS nodes to use: Sprite (image), Label
   (text), Path/shape vectors, Group (container).
3. **Animation style** (optional) -- keyframe sequence, duration, easing,
   iteration count. Default to smooth looping if not specified.
4. **Interactivity** (optional) -- click, hover, drag behaviors on elements.
5. **Canvas dimensions** (optional) -- default 800x600 if not specified.
6. **Image assets** (optional) -- URLs for sprite textures or backgrounds.

## SpriteJS API reference

### Include script (CDN)

```html
<script src="https://unpkg.com/spritejs@3/dist/spritejs.js"></script>
```

After loading, destructure from the global `spritejs`:

```javascript
const { Scene, Sprite, Label, Path, Group, Polyline, Ellipse,
        Arc, Ring, Regular, Triangle, Rect, Star } = spritejs;
```

### Scene setup

```javascript
const container = document.getElementById('stage');
const scene = new Scene({
  container,
  width: 800,
  height: 600,
  // mode: 'scale' | 'stickyWidth' | 'stickyHeight' | 'stickyTop' | 'stickyBottom'
});
const layer = scene.layer();  // default layer
```

`mode` controls viewport scaling:
- `scale` -- uniform fit
- `stickyWidth` / `stickyHeight` -- lock one axis, scale the other
- `stickyTop` / `stickyBottom` / `stickyLeft` / `stickyRight` -- anchor edge

### Creating elements

**Sprite (image)**:

```javascript
const sprite = new Sprite({
  texture: 'https://example.com/image.png',
  pos: [100, 200],
  size: [64, 64],
  anchor: [0.5, 0.5],
});
layer.append(sprite);
```

**Label (text)**:

```javascript
const label = new Label('Hello SpriteJS');
label.attr({
  font: '42px Arial',
  fillColor: '#ff5722',
  pos: [400, 50],
  anchor: [0.5, 0],
});
layer.append(label);
```

**Vector shapes (Path, Polyline, Ellipse, Arc, Ring, Rect, Star, etc.)**:

```javascript
const circle = new Ellipse({
  radiusX: 50,
  radiusY: 50,
  pos: [300, 300],
  fillColor: '#2196f3',
  strokeColor: '#0d47a1',
  lineWidth: 3,
});
layer.append(circle);

const star = new Star({
  angles: 5,
  innerRadius: 20,
  outerRadius: 50,
  pos: [500, 300],
  fillColor: '#ffc107',
});
layer.append(star);

const path = new Path({
  d: 'M 0,0 L 100,0 L 100,100 Z',
  pos: [100, 400],
  fillColor: '#4caf50',
  strokeColor: '#1b5e20',
  lineWidth: 2,
});
layer.append(path);
```

**Group (container)**:

```javascript
const group = new Group({
  pos: [200, 200],
  anchor: [0.5, 0.5],
});
group.append(child1);
group.append(child2);
layer.append(group);
```

### Common attributes

| Attribute | Type | Description |
|---|---|---|
| `pos` | `[x, y]` | Position |
| `size` | `[w, h]` | Dimensions |
| `anchor` | `[ax, ay]` | Anchor point (0-1) |
| `scale` | `[sx, sy]` or number | Scale transform |
| `rotate` | number (degrees) | Rotation |
| `opacity` | 0-1 | Transparency |
| `fillColor` | string | Fill color |
| `strokeColor` | string | Stroke color |
| `lineWidth` | number | Stroke width |
| `bgcolor` | string | Background color |
| `borderRadius` | number | Corner radius |
| `zIndex` | number | Stacking order |

### Animation

SpriteJS uses a Web Animations API-compatible `.animate()` method:

```javascript
sprite.animate([
  { pos: [0, 0], rotate: 0 },
  { pos: [600, 0], rotate: 180 },
  { pos: [600, 400], rotate: 360 },
], {
  duration: 3000,
  iterations: Infinity,
  direction: 'alternate',
  easing: 'ease-in-out',
  fill: 'forwards',
});
```

**Transition (single-property tween)**:

```javascript
sprite.transition(0.5);  // 0.5s transition
sprite.attr({ pos: [400, 300], opacity: 0.5 });
```

### Event handling

```javascript
sprite.addEventListener('click', (evt) => {
  console.log('clicked at', evt.x, evt.y);
});

sprite.addEventListener('mouseenter', () => {
  sprite.attr({ scale: 1.2 });
});

sprite.addEventListener('mouseleave', () => {
  sprite.attr({ scale: 1.0 });
});
```

Supported events: `click`, `mousedown`, `mouseup`, `mousemove`,
`mouseenter`, `mouseleave`, `touchstart`, `touchend`, `touchmove`.

### Resource preloading

```javascript
await scene.preload({
  id: 'mySheet',
  src: 'https://example.com/spritesheet.png',
  // or for JSON atlas:
  // src: 'https://example.com/atlas.json'
});
```

### Multiple layers

```javascript
const bgLayer = scene.layer('bg');
const fgLayer = scene.layer('fg');
// Lower layer renders behind; higher renders in front
```

## Output format

Produce a single self-contained HTML file that:

1. Includes the SpriteJS v3 CDN script.
2. Contains a `<div id="stage">` container.
3. Creates a `Scene` bound to the container with appropriate dimensions.
4. Builds the requested elements and appends them to layers.
5. Applies animations using `.animate()` or `.transition()`.
6. Wires up any requested event handlers.
7. Is responsive -- uses `mode: 'scale'` or percentage-based container sizing.
8. Includes brief inline comments explaining each animated element.

## Quality checks

Before delivering, verify:

- The scene container exists in the HTML before the script runs (use
  `DOMContentLoaded` or place the script after the container).
- All image URLs are reachable public HTTPS URLs (no local `file://` paths).
- Animation durations are in milliseconds (not seconds).
- `anchor` values are between 0 and 1.
- Vector shape attributes match SpriteJS v3 API (e.g. `radiusX`/`radiusY`
  for Ellipse, not `radius`).
- Event listeners use lowercase event names without the `on` prefix.
- No raw `canvas.getContext('2d')` calls -- all rendering goes through
  SpriteJS nodes.
