# CLAUDE.md

Guidance for Claude Code working in this repository.

## What this is

A single-file WebGL2 fire simulation (`index.html`). Buoyant incompressible
Navier–Stokes + vorticity confinement + a fuel/temperature/soot combustion
model + blackbody rendering. No build step, no dependencies. See README.md for
the equations and the solver order.

## Working on it

- Serve it (`python3 -m http.server`) rather than opening `file://` — some
  tooling refuses file URLs.
- **Do not tune by eye.** `window.fire.run(seconds)` and `window.fire.stats()`
  make the simulation scriptable and deterministic. Every parameter default in
  `P` came from a sweep over `hotFrac`, `flameTop`, `flameWidth` and, critically,
  `vyDown` — the downward mass flux, which is the early-warning signal for the
  confinement instability.
- **Stability is a 120-second property, not a 10-second one.** Several
  configurations look perfect for 15 s and are dead by 60 s. Any parameter
  change touching `conf`, `buoy`, `vdamp` or `sootw` needs a long run.
- `vyDown` should stay below ~0.05. If it climbs and plateaus around 0.7, the
  vorticity confinement is pumping a recirculation — lower `conf`.

## GLSL ES 3.00 gotchas hit here

`active`, `patch` and `sample` are reserved words. `vec4` has no `.c` swizzle.
Half-float textures are linearly filterable in WebGL2 core; 32-bit float ones
need `OES_texture_float_linear`, which is why everything here is `*16F`.

## Related

`~/git/fluid-dynamics/` — the vorticity–streamfunction GPU solver this borrows
its Jacobi-projection structure from.
