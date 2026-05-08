# aframe-rapier-examples

Two known-good standalone HTML demos of driving a car around an A-Frame
scene with [Rapier](https://rapier.rs/) physics. Each is a single
self-contained `index.html` — no build step, no npm, just open in a
browser (or serve over HTTP).

Both demos share the same scaffolding (80×80 ground, perimeter walls,
8 parked cars, top-down WASD camera, live tuning panel) so you can A/B
the two techniques back-to-back.

## Demos

### [`rapier-arcade-body/`](./rapier-arcade-body/index.html)

Single dynamic rigid body driven by `setLinvel` / `setAngvel` directly —
no per-wheel physics. Speed-locked steering, X/Z rotation locked so the
car can't tip. Snappy and reliable.

Uses the [`aframe-rapier-physics`](https://github.com/Elettrotecnica/aframe-rapier-physics)
A-Frame wrapper.

**Best for:** arcade-feel gameplay, top-down driving, vehicles where
realistic suspension/wheel behavior doesn't matter.

### [`rapier-vehicle/`](./rapier-vehicle/index.html)

Proper raycast-wheel vehicle using Rapier's
[`DynamicRayCastVehicleController`](https://rapier.rs/javascript3d/classes/DynamicRayCastVehicleController.html).
Per-wheel suspension, friction slip, side friction, engine force, brake
force, steering. Front-wheel drive, all-wheel brake, front steer.

Imports `@dimforge/rapier3d-compat` directly via ES modules and runs its
own `World.step()` from a single A-Frame component tick — bypasses the
A-Frame wrapper entirely so the vehicle controller has unmediated
access to the physics world.

**Best for:** physically-grounded driving, anything where you want
suspension travel, wheelies, slides, ramp launches.

## Running

Either demo needs to be served over HTTP (ES modules + WebAssembly
won't run from a `file://` URL):

```sh
python3 -m http.server 8765
# then open http://localhost:8765/rapier-arcade-body/
#         or http://localhost:8765/rapier-vehicle/
```

Controls (both demos):
- **WASD** / **arrow keys** — drive
- **Space** — brake (vehicle demo only)
- **R** — reset (vehicle demo only)

## Original sources

- **`rapier-vehicle`** is a literal port of Isaac Mason's
  [`dynamic-raycast-vehicle-controller` sketch](https://github.com/isaac-mason/sketches/tree/main/sketches/rapier/dynamic-raycast-vehicle-controller)
  (chassis dimensions, wheel positions, all `setWheel*` tuning calls,
  engine/brake/steer defaults, and per-frame update order copied
  byte-for-byte). The reference itself credits
  [michael-go/raphcar](https://github.com/michael-go/raphcar) for the
  vehicle pattern.
- **`rapier-arcade-body`** was written from scratch but uses
  [Elettrotecnica's `aframe-rapier-physics` wrapper](https://github.com/Elettrotecnica/aframe-rapier-physics)
  (commit `43f389f4`) for the `rapier-body` / `rapier-shape` components.
- **A-Frame** is pinned to commit
  [`6a054e85`](https://github.com/aframevr/aframe/commit/6a054e85953bbcc71854bcd64f9abde0897be649)
  (master build) for Three.js r181 compatibility.

## Why two demos?

The arcade-body approach was built first, works great, and is the
recommended starting point for most use cases. The raycast-vehicle
demo exists because a previous attempt to wire a `DynamicRayCastVehicleController`
through the A-Frame wrapper hit an undiagnosable energy sink — the
chassis would creep at ~0.15 m/s no matter what engine force was
applied. Going direct to Rapier and porting a known-working reference
(rather than tuning from first principles) was the path that worked.
The header comment in `rapier-vehicle/index.html` walks through this.

## License

MIT for the demo code in this repo. The vendored libraries follow
their own licenses (A-Frame: MIT, Rapier: Apache-2.0).
