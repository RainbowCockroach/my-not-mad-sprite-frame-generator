# my-not-mad-sprite-frame-generator

A one-page utility for drawing furniture sprites for **my-not-mad-2.5** (Unity, 2.5D top-down, orthographic camera at 45° yaw / 30° tilt, PPU 128).

Type the footprint a piece of furniture occupies — **width (X)**, **depth (Z)**, **height (Y)** in world units — and it gives you:

- the exact canvas size in pixels, and
- a transparent PNG guide with the projected box wireframe: footprint diamond flush to the canvas bottom, the vertical edges, and the top face. Hidden edges are dashed.

Drop the PNG into your art tool as its own layer, trace the silhouette, then delete the layer. The guide is drawn so the near footprint corner sits on the bottom edge and the drawing is horizontally centred — the anchoring `Furniture.prefab` expects (export at PPU 128, Pivot Bottom, Filter Point).

## Live app

https://rainbowcockroach.github.io/my-not-mad-sprite-frame-generator/

## Projection

Screen offset per world unit, with `S` = pixels per unit, `ψ` = camera yaw, `θ` = camera tilt:

| axis | across | up |
|---|---|---|
| ground X | `cos ψ · S` | `sin θ · sin ψ · S` |
| ground Z | `−cos ψ · S` | `sin θ · sin ψ · S` |
| height Y | `0` | `cos θ · S` |

which gives `90.5`, `45.3` and `110.9` px at the project's current camera. Canvas is then
`(W + D) · across` wide by `(W + D) · rise + H · up` tall.

Height uses `cos θ` rather than the full `S` because `CameraFacingBillboard` cancels the tilt squash, so one drawn pixel is one screen pixel — a sprite therefore has to match what the camera actually renders for 3D geometry of the same height.

The **Legacy 30° lines** preset reproduces the older `90 / 52 / 128` numbers recorded in the game repo's `CLAUDE.md`, for comparing against sprites already drawn to them.

Yaw, tilt and pixels-per-unit are editable, so a camera change means changing the numbers here, not the code.

## Running it

Static single file, no build step. Open `index.html`, or serve the folder with anything.
