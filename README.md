# Paleblood Vigil

> *"The Great Ones as gravitational nightmares. Blood as substrate of cosmic computation. Every particle a prayer drawn into the abyss."*

A generative art piece built with [p5.js](https://p5js.org/), inspired by the cosmic horror and gothic atmosphere of *Bloodborne*. Open `index.html` in any browser — no build step, no server, no dependencies beyond a CDN script tag.

---

## What it does

The canvas runs two concurrent algorithmic systems that interact to produce emergent visual complexity:

### 1. Blood Fluid (N-body attractor simulation)

2000+ particles, each with a velocity vector, are released across the canvas and subjected to:

- **Inverse-square attractor gravity** — four *Nightmare Entities* orbit slowly around the canvas centre. Each exerts a gravitational pull on every particle: `force = (pull × strength × pulse) / distance²`, capped to prevent singularity blow-up.
- **Perlin flow field** — layered Perlin noise steers particles with an additional angular perturbation each frame, producing organic turbulence on top of the gravitational structure.
- **Velocity damping** — configurable viscosity damps all velocity each tick, preventing unbounded acceleration.
- **Velocity-encoded colour** — each particle's HSB hue, saturation, and brightness are computed from its instantaneous speed: resting particles render in deep crimson `(hue ≈ 0°)`, fast-moving ones shift toward blood-orange `(hue ≈ 20°)` with proportionally higher brightness. This makes density and motion readable at a glance.

Trails are rendered onto a persistent off-screen `p5.Graphics` buffer (`trailPg`). Each frame the buffer is dimmed by a small semi-transparent fill (the *Trail Fade* parameter), so old paths slowly decay rather than vanishing immediately. The result is a heat-map-style accumulation of blood where particles congregate.

### 2. Tentacles (Chain IK from screen edges)

Up to 20 tentacles spawn from random points on the four canvas edges and grow inward. Each tentacle is a chain of `N` segments (14–28, randomised per instance) updated with:

- **Noise-steered head** — the leading segment's angle is driven by a Perlin noise sample, producing smooth organic wandering. The steering uses shortest-arc angular interpolation to avoid flipping: `diff = ((target − angle + 3π) mod 2π) − π`.
- **FABRIK-lite constraint propagation** — after the head moves, each subsequent segment checks its distance to the previous one. If it exceeds `segLen`, it is pulled along the connecting vector by `(distance − segLen) / distance × 0.5`, equivalent to one iteration of the Forward And Backward Reaching Inverse Kinematics algorithm.
- **Tapered rendering** — stroke weight decreases linearly from root (`thick`) to tip (`thick × 0.25`). Each segment gets a sinusoidal brightness pulse keyed to `frameCount + segmentIndex`, creating a slow undulating bioluminescence.
- **Sucker nodes** — every 5 segments, an additional filled circle is drawn at the joint position, simulating the suckers of a cephalopod appendage.
- **Lifecycle fade-in / fade-out** — alpha ramps up over 50 frames at birth and down over 70 frames before death, preventing hard pops.

When a tentacle exceeds its `maxAge`, it is removed. The spawn loop immediately replaces it, maintaining the configured tentacle count at all times.

### 3. Eldritch Sigil (background geometry)

Three concentric nested polygons (hexagon, nonagon, dodecagon) rotate at slightly different speeds and directions around the canvas centre. Alpha is ≈ 3–7%, making them nearly invisible — present as subliminal geometry rather than overt decoration. Spokes radiate from the centre to each vertex.

---

## Architecture

```
index.html
└── <script>
    ├── P {}                  — global parameter object, mutated by UI
    ├── class Attractor       — orbiting gravity source
    ├── class Particle        — blood droplet with velocity + trail rendering
    ├── class Tentacle        — chain IK segment array
    ├── p5 instance
    │   ├── setup()           — canvas + trailPg creation, calls init()
    │   ├── draw()            — frame loop: fade → update → composite → vignette
    │   ├── drawSigil()       — background eldritch geometry
    │   └── init()            — full reset: seed all PRNGs, spawn entities
    └── UI handlers           — up(), changeSeed(), doReinit(), doPNG(), doReset()
```

Single self-contained HTML file. No build tooling, no framework, no local server required.

---

## Algorithmic Art Methodology

This project was generated using a two-phase *algorithmic philosophy* workflow:

**Phase 1 — Philosophy.** Before writing a line of code, a computational aesthetic manifesto (*"Vileblood Hymn"*) was written. This philosophy defines the movement's conceptual DNA: what mathematical processes express the theme, what emergence patterns are sought, what the algorithm should *feel* like at runtime. It answers "why" before "how".

**Phase 2 — Implementation.** The philosophy is then translated into code. Parameters, system counts, colour mappings, and interaction rules are all derived from the philosophy rather than chosen arbitrarily. The algorithm is the philosophy made executable.

This separation — thinking before coding — is what distinguishes generative art from procedural noise. The same approach produces different results with every philosophy, even using identical p5.js primitives.

---

## Parameters

| Parameter | Range | Effect |
|---|---|---|
| **Blood Count** | 500 – 4000 | Total live particle count |
| **Gravity Pull** | 0.2 – 3.0 | Attractor force multiplier |
| **Turbulence** | 0.5 – 8.0 | Perlin noise scale (higher = more chaotic flow) |
| **Tentacles** | 2 – 20 | Simultaneous tentacle count |
| **Viscosity** | 0.5 – 6.0 | Velocity damping per frame |
| **Trail Fade** | 2 – 30 | Trail persistence (lower = longer memory) |

All parameters are live — changes take effect on the next frame without reinitialisation.

---

## Seed System

The seed controls `p5.randomSeed()` and `p5.noiseSeed()` simultaneously, making every visual output fully reproducible. Two runs with the same seed and parameters produce identical results.

- **Prev / Next** — increment/decrement seed by 1
- **Random Nightmare** — jump to a random seed in [1, 99999]
- **Jump to seed** — enter any integer

The seed space is effectively infinite. Each seed produces a unique attractor configuration, particle distribution, and tentacle topology.

---

## Usage

```bash
# Clone and open
git clone https://github.com/MaximilianWik/paleblood-vigil.git
cd paleblood-vigil
open index.html   # or double-click in file explorer
```

No `npm install`. No webpack. No server. It runs.

---

## Technical stack

| | |
|---|---|
| Rendering | [p5.js 1.9.4](https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.4/p5.min.js) via CDN |
| Noise | p5's built-in Perlin noise (`p5.noise`) |
| Colour model | HSB, 360° hue, set via `p5.colorMode(HSB)` |
| Off-screen buffer | `p5.createGraphics()` for trail accumulation |
| Typography | [Cinzel](https://fonts.google.com/specimen/Cinzel) + [Crimson Text](https://fonts.google.com/specimen/Crimson+Text) via Google Fonts |
| Distribution | Single `.html` file, fully self-contained |

---

## License

MIT
