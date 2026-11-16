# my-not-mad-sprite-frame-generator

Two guide-sheet generators for **my-not-mad-2.5** (Unity, 2.5D top-down, orthographic camera at 45° yaw / 30° tilt, PPU 128). Both give you an exact-size canvas and a transparent PNG to trace over, then delete.

## Furniture tab

Type the footprint a piece occupies — **width (X)**, **depth (Z)**, **height (Y)** in world units — and get the projected box: footprint diamond flush to the canvas bottom, the vertical edges, the top face, hidden edges dashed.

The guide is drawn so the near footprint corner sits on the bottom edge and the drawing is horizontally centred — what `Furniture.prefab` expects (import at PPU 128, Pivot Bottom, Filter Point).

## Character tab

Type the character's **height in metres** (one world unit is one metre, measured on screen next to walls and furniture) and how many **heads tall** it is (3 for this game's chibi style, 7.5 for a realistic adult), and you get a frame to draw in. Everything else has a working default under **Sheet & guides**. The frame carries:

- a **ground line** on the cell's bottom edge — the bottom-centre pivot, so the feet go here;
- a **crown line** at `height × 110.9` px, which is how tall the figure has to be drawn to stand correctly next to 3D walls and furniture;
- a **facing arrow** on the floor and a **row label**, so each row says which way the character looks. The arrow follows the camera's 2:1 ground angle, and points screen-left by default because `PlayerVisual` sets `flipX` when moving camera-right — the art is drawn facing left and mirrored at runtime. A label containing “back” aims it away from the camera, “right” mirrors it;
- a dashed **stance ellipse** — the CapsuleCollider's radius projected onto the floor, so you can see how wide a pose can get before the drawing overhangs what actually collides.

The default sheet is **6 × 4 cells of 256 px**: one animation per row, six frames across, in the order front idle, front walk, back idle, back walk. Under **Sheet & guides** you can change columns, rows and cell size, set the stance radius, or switch on **Head lines** to divide the figure into head-heights.

Character height is the *drawn* height, not the collider height. In a top-down game only the capsule's radius really affects gameplay, so a taller character is mostly a drawing change.

### Gait: keeping the feet from sliding

A walk looks glued to the floor only when the body moves exactly one step length per drawn step. Faster than that and the feet skate. Type the character's **height**, **heads tall** and **walk speed** (1.4 m/s is an everyday adult pace) and the tool works out the rest:

- **Leg length** comes from heads tall: hip height is 25% of height at 2 heads, 33% at 3 heads (chibi) and 49% at 7.5 heads (realistic), with straight lines in between.
- **Step length** = `0.85 × leg × √(speed / 1.4)`. Steps follow the legs, not the height, so a chibi takes short, quick steps: a 1.72 m, 3-head figure steps 0.48 m at 1.4 m/s, while a realistic one steps 0.72 m. Set **Step length** under Sheet & guides to override it for the walk.
- **Samples**: the clip's frame rate, so one loop (two steps) covers exactly two step lengths: `fps = columns × speed / (2 × step)`, rounded.
- **Base speed**: the speed that rounded clip really animates at, `2 × step × samples / columns`. In Unity the walk state's speed multiplier is `current speed / base speed`, so the feet stay matched at any speed.

Rows labelled "run" use **Run speed** instead. The readout warns when a clip falls under 6 fps (choppy, so draw more frames) or over 15 fps (frantic). Short chibi legs make a fast run frantic quickly, so the warning names the column count for a separate run sheet.

**Pose template** draws a ghost mannequin in every cell. The mannequin uses the same head count and leg length as the gait math. Walk and run rows get one pose per frame (contact, passing, contact, passing) with the feet on the projected floor exactly one step apart; other rows stand. Joints are solved in metres and then projected, so the knees stay correct for a figure walking away. Treat the run poses as a rough guide.

**Floor margin** leaves room under the feet, because a figure walking toward the camera puts its near foot *below* its own pivot on screen. The readout gives the Unity pivot to use (Custom, X 0.5, Y = margin ÷ cell) and warns when the near foot would be clipped.

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
