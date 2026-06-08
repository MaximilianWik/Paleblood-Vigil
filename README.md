# Paleblood Vigil

Generative art built with [p5.js](https://p5js.org/). Open `index.html` in any browser — no build step, no server, no dependencies beyond a CDN script tag.

---

## What it does

### Blood Fluid (N-body attractor simulation)

Particles with velocity vectors are released across the canvas and subjected to:

- **Inverse-square attractor gravity** — four attractors orbit slowly around the canvas centre, each exerting gravitational pull: `force = (pull × strength × pulse) / distance²`, capped to prevent singularity blow-up. Each attractor oscillates in strength via a sine pulse.
- **Perlin flow field** — layered Perlin noise adds an angular perturbation each frame, producing organic turbulence on top of the gravitational structure.
- **Velocity damping** — configurable viscosity damps all velocity each tick.
- **Velocity-encoded colour** — HSB hue, saturation, and brightness are computed from instantaneous speed: slow particles render in deep crimson `(hue ≈ 0°)`, fast ones shift toward blood-orange `(hue ≈ 20°)` with proportionally higher brightness.

Trails accumulate on a persistent off-screen `p5.Graphics` buffer (`trailPg`). Each frame the buffer is dimmed by a semi-transparent fill (the *Trail Fade* parameter), so old paths decay gradually. This produces heat-map-style accumulation at attractor convergence zones.

### Geometric Substrate

Three concentric nested polygons (hexagon, nonagon, dodecagon) counter-rotate at slightly different speeds around the canvas centre. Spokes radiate from centre to each vertex. Alpha is set low (10–22%) so the geometry reads as structural substrate beneath the particle layer rather than foreground decoration.

---

## Architecture

```
index.html
└── <script>
    ├── P {}              — global parameter object, mutated live by UI
    ├── class Attractor   — orbiting gravity source with sinusoidal pulse
    ├── class Particle    — velocity particle with trail rendering to trailPg
    ├── p5 instance
    │   ├── setup()       — canvas + trailPg creation, calls init()
    │   ├── draw()        — fade → update → composite → attractor glow → sigil → vignette
    │   ├── drawSigil()   — rotating nested polygon geometry
    │   └── init()        — full reset: seed PRNGs, spawn attractors + particles
    └── UI handlers       — up(), syncUI(), randParams(), doReinit(), doPNG(), doReset()
```

Single self-contained HTML file. No build tooling, no framework, no local server required.

---

## Algorithmic Art Methodology

Generated using a two-phase workflow:

**Phase 1 — Philosophy.** A computational aesthetic manifesto is written before any code. It defines what mathematical processes express the theme, what emergence patterns are sought, and what the algorithm should feel like at runtime.

**Phase 2 — Implementation.** The philosophy is translated into code. Parameters, colour mappings, and interaction rules are derived from the philosophy rather than chosen arbitrarily. The algorithm is the philosophy made executable.

This separation — thinking before coding — distinguishes generative art from procedural noise. The same approach produces fundamentally different systems with every new philosophy, even using identical p5.js primitives.

---

## Parameters

| Parameter | Range | Effect |
|---|---|---|
| **Blood Count** | 500 – 4000 | Total live particle count |
| **Gravity Pull** | 0.2 – 3.0 | Attractor force multiplier |
| **Turbulence** | 0.5 – 8.0 | Perlin noise scale — higher = more chaotic flow |
| **Viscosity** | 0.5 – 6.0 | Velocity damping per frame |
| **Trail Fade** | 2 – 30 | Trail persistence — lower = longer memory |

All parameters are live — changes take effect immediately without reinitialisation.

**Randomize Params** randomises all five simultaneously, then reinitialises.

---

## Seed System

`p5.randomSeed()` and `p5.noiseSeed()` are set together from a single seed value, making every visual output fully reproducible. Same seed + same parameters = identical output.

- **Prev / Next** — increment/decrement seed by 1
- **Random Seed** — jump to a random seed in `[1, 99999]`
- **Jump to seed** — enter any integer directly

---

## Usage

```bash
git clone https://github.com/MaximilianWik/paleblood-vigil.git
cd paleblood-vigil
open index.html
```

No `npm install`. No webpack. No server.

---

## Technical stack

| | |
|---|---|
| Rendering | [p5.js 1.9.4](https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.4/p5.min.js) via CDN |
| Noise | p5 built-in Perlin noise |
| Colour model | HSB 360° via `p5.colorMode(HSB)` |
| Off-screen buffer | `p5.createGraphics()` for trail accumulation |
| Typography | [Cinzel](https://fonts.google.com/specimen/Cinzel) + [Crimson Text](https://fonts.google.com/specimen/Crimson+Text) |
| Distribution | Single `.html`, fully self-contained |

---

## License

MIT
