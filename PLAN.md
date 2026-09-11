# PLUMBING — plan and build notes

## §1 Why this model exists
Josias asked for "a visual model just for the plumbing on a house," referring to the
other models. Scope settled in two questions: **all three systems** (DWV, water supply,
gas — whole house, tap to trim) and **one clean path**, the same decision that was made
for HOUSE Block on 7 Sep 2026: a beginner level gets ONE road, and every fork goes on a
separate Advanced level.

## §2 What it is built on
The HOUSE `apps/cbs3d` engine, unchanged where it could be. Replaced: the card catalog,
the card pictures (`ART`), the whole 3D section, the inspection rows. Back-ported from
HOUSE Advanced at the same time, because all three bugs live in `cbs3d`:

1. **The deck never re-seeks after a FAILED gate** — the pass branch calls `deckSeek()`,
   the fail branch returned before it and could strand the player on a dead card.
2. **The read-out panel landed on top of the next card** — anchored to a parked ring
   tile in deck mode. Now `placePeek()` anchors to the deck and clamps away from it.
3. **A card whose subject is off-screen produced no visible change** — `flyTo` now
   animates the camera *target*, and a `SHOT` table gives the job sign, the sewer tap,
   the meter box, the roof vents, the gas meter and the hose bibb their own camera.

## §3 The one deliberate departure from HOUSE
HOUSE's "Cutaway" is a `THREE.Plane` clipping the model in half at z = 0. Three of the
seven plumbing steps happen under the slab and the rest are spread across both halves of
the house, so a clipping plane hides the lesson. **X-ray** replaces it: the shell layers
(`shell`, `shell2`, `shell3`, `closed`) plus the ground (`pad`) drop to 14% opacity with
`depthWrite` off. `renderer.localClippingEnabled` is now always `false`.

The ground is its own material group (`pad`, not `site`) for exactly this reason — an
opaque lawn buries steps 1–3.

## §4 The seven gates
Taken verbatim off the 2019 BRCOM scheduling sheet, which lists 303 Sewer Connection,
750 Utilities Sewer Cleanout, 301 Plumbing Underground Rough (priority 1), 503 Slabs
(priority 2), 304 Water Connection, 751 Utilities Water Meter Box, 311 Plumbing Rough
(Top Out) (priority 7), 400 Gas Piping - Rough, and 999 Final (priority 11).
313 Septic Tank Connection exists on the same sheet and is the fork that belongs in
Advanced — this model takes city sewer.

## §5 The traps
A trap must share the honest card's `needs` slot (`altOf`) so only one of the pair can
go on the house, and the gate that catches it lists it in `failsIf`.

- `st` **sanitary tee on its back** pairs with `br` wye-and-eighth-bend. Burned by `i2` (301).
- `va` **vents capped in the attic** pairs with `vr` vents through the roof. Burned by `i5` (311).

Verified in a real browser: the honest run is 37 placements, 7/7 gates, 0 leaks,
0 fails. The trap run takes both lies, fails two gates, burns both cards, and still
finishes after the rework.

## §6 The plan is data
- `PARTS_W` — the floor plan. A wet wall at z = -5 built deeper on purpose (a 3 in.
  drain does not fit in a 3½ in. stud), two baths behind it, kitchen front-right,
  laundry and water heater front-left.
- `ND` — the drain tree, node by node, `[x, y, z]`. The `y` is the real fall: about
  8 in. from the back bathroom to the exit at the front, which is 1/8 in. per foot
  across roughly 30 ft.
- `STUBS` — every stub-up, its size, and what sits on it. Everything downstream (the
  branch fitting, the cap, the sleeve, the trap arm, the vent, the nail plate, the
  fixture, the wet trap at the end) is generated off this one list.
- `R4 / R3 / R2` — 4, 3 and 2 in., drawn oversize so they read apart on screen. ◈ Model.

## §7 The helper this model is built on
```js
pipe(a, b, r, m, g)   // a cylinder from point A to point B at any angle
```
HOUSE only ever needed axis-aligned pipe. Slope is the whole lesson here, so every run
is drawn between two real points and the fall is visible. `run(pts, r, m, g)` chains
them with hubs at the joints.

## §8 Still open
1. **Advanced** — the forks: PEX manifold vs CPVC trunk, tank vs tankless, city sewer
   vs septic (which adds 313 and drops 303/750), cast iron vs PVC. Scored in days and
   dollars like HOUSE Advanced.
2. **Repipe** — a finished house with failing pipe in the walls.
3. **Condo stack** — eight units on one stack, where the shutdown is the whole problem.
4. An artifact copy of `apps/pipe` for sharing outside GitHub Pages.
5. A card for the **shower valve rough-in depth** measured off finished tile is inside
   `sv`; it may deserve its own card once somebody plays it twice.

## §9 Gotchas that cost time here
- `shellW()` splits a wall around its door and window openings. Passing it a 3/4 in. tall
  chalk strip turned every opening into a 1 ft. panel standing on the pad. Draw plain
  strips instead.
- Nothing may touch `THREE` at top level — `const UP = new THREE.Vector3(...)` threw
  before the no-WebGL fallback could run, and killed the page on a machine without it.
- A vent has to stand proud of the *shingle* surface, which is `ROOFY(z) + lift`, not
  `ROOFY(z)`. The first version buried all six vents inside the roof.
- The container's headless Chromium cannot reach cdnjs. Test with a Playwright route
  that fulfils `**/three.min.js` from a local copy; verify the real page in his Chrome.
- Card text is single-quoted JS — **curly apostrophes only**.
