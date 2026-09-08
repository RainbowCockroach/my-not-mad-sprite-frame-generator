# my-not-mad-sprite-frame-generator

Two guide-sheet generators for **my-not-mad-2.5** (Unity, 2.5D top-down, orthographic camera at 45° yaw / 30° tilt, PPU 128). Both give you an exact-size canvas and a transparent PNG to trace over, then delete.

## Furniture tab

Type the footprint a piece occupies — **width (X)**, **depth (Z)**, **height (Y)** in world units — and get the projected box: footprint diamond flush to the canvas bottom, the vertical edges, the top face, hidden edges dashed.

The guide is drawn so the near footprint corner sits on the bottom edge and the drawing is horizontally centred — what `Furniture.prefab` expects (import at PPU 128, Pivot Bottom, Filter Point).

## Character tab

Type the character's **height in metres** — one world unit is one metre — and get a frame to draw in. That is the only field the artist needs; everything else has a working default under **Sheet & guides**. The frame carries:

- a **ground line** on the cell's bottom edge — the bottom-centre pivot, so the feet go here;
- a **crown line** at `height × 110.9` px, which is how tall the figure has to be drawn to stand correctly next to 3D walls and furniture;
- a dashed **stance ellipse** — the CapsuleCollider's radius projected onto the floor, so you can see how wide a pose can get before the drawing overhangs what actually collides.

The default sheet is **4 × 4 cells of 256 px**, matching `akane_normal.png`: one animation per row, four frames across. Under **Sheet & guides** you can change columns, rows and cell size, set the stance radius, or turn on head-division lines for figure proportions.

Character height is the *drawn* height, not the collider height. In a top-down game only the capsule's radius really affects gameplay, so a taller character is mostly a drawing change.

## Live app

https://rainbowcockroach.github.io/my-not-mad-sprite-frame-generator/

## Projection

Screen offset per world unit, with `S` = pixels per unit, `ψ` = camera yaw, `θ` = camera tilt:

| axis | across | up |
|---|---|---|
| ground X | `cos ψ · S` | `sin θ · sin ψ · S` |
| ground Z | `−cos ψ · S` | `sin θ · sin ψ · S` |
| height Y | `0` | `cos θ · S` |

which gives `90.5`, `45.3` and `110.9` px at the project's current camera — checked against Game view. Canvas is then
`(W + D) · across` wide by `(W + D) · rise + H · up` tall.

Height uses `cos θ` rather than the full `S` because `CameraFacingBillboard` cancels the tilt squash, so one drawn pixel is one screen pixel — a sprite therefore has to match what the camera actually renders for 3D geometry of the same height.

Yaw, tilt and pixels-per-unit are editable, so a camera change means changing the numbers here, not the code.

## Running it

Static single file, no build step. Open `index.html`, or serve the folder with anything.
