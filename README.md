# PLUMBING — Rough to Trim

The whole plumbing system of a Treasure Coast CBS house as a 3D training model.
Built for **Covenant Builders** (CBC1253676, Vero Beach) to train crews and to give a
superintendent one screen that shows *why* the plumber has to come back three times.

**Live:** https://kingofthewisdomrealm-hub.github.io/PLUMBING/ → `apps/pipe/`

## What it is

40 cards. 7 county inspections. 2 traps you cannot see from the street.

You drag the top card onto the house. A card only goes on if what it needs is already
there — try it early and you get a leak, not a lecture. Every inspection is a card too,
because **calling the inspection is a step**, not something that happens to you.

### The seven gates, in Indian River County order

| | Code | Gate | What he sees |
|---|---|---|---|
| 1 | `303 · 750` | Sewer connection & cleanout | A tap into the main, a cleanout at the property line, nothing covered |
| 2 | `301` | Plumbing underground rough | Every pipe under the slab, open, capped, holding the test |
| 3 | `503` | Slab | Your stub-ups capped, plumb, braced — his inspection, your neck |
| 4 | `304 · 751` | Water connection & meter box | A meter box at the line, an open trench, the line holding pressure |
| 5 | `311` | Plumbing rough (top out) | Drain, vent and water in open walls, plated at every stud |
| 6 | `400` | Gas piping rough | Gas pipe exposed, capped, with a gauge that has not moved |
| 7 | `999` | Final | Every fixture set, every trap wet, every stop at 120 |

### The two traps

Both look completely reasonable, both are invisible once they are covered, and each one
is burned out of the house by the gate that catches it.

- **A sanitary tee laid on its back** under the slab, instead of a wye and an eighth
  bend. It glues, it holds a water test, and it turns the branch straight into the side
  of the main. Fails `301`.
- **Six vents that stop in the attic and get capped.** It is raining, the roofer is a
  week out, and nobody sees a capped pipe in an attic full of trusses. Fails `311`.

### The four views

- **X-ray** (default) — the slab, the block and the drywall go to glass, and the **frame
  is hidden outright**. A thousand ghosted trusses and studs stack into a haze you cannot
  see through, so anything built into a `denseGroup()` only exists in Street. This is the
  one change from HOUSE: HOUSE cuts the model in half with a clipping plane, which would
  hide half the plumbing.
- **Pipes only** — the building and the ground go away completely. What is left is the
  system: every pipe, every fitting, every fixture, hanging in the air the way it does on
  a riser diagram. Trench dirt is tagged `soilMat()` so it goes too.
- **Street** — what a homeowner sees. A tee on its back looks exactly like this too.
- **Inspector** — only what the open gate checks is lit.

**Zoom** — drag the bar in the bottom-left corner, or use the − and + buttons. The wheel
and a two-finger pinch still work; all of them write to the same `cam.r` and the bar
follows.

---

# The Fixer — six calls, five years later

**Live:** https://kingofthewisdomrealm-hub.github.io/PLUMBING/ → `apps/fix/`

Same house. Now it is lived in and the phone rings. **47 cards, 6 calls, 6 shortcuts.**

**Six calls, six decks.** The model opens on the board, not in a room. You pick the call
you are standing in front of and the deck holds only that call's cards; you can work them
in any order, leave one half-done and come back to it, and the money runs across the whole
day. Close a call and the board comes back with it marked.

Every call runs the same five beats — **Call → Look → Find → Fix → Prove** — with a **Tell**
card where there is a number the homeowner needs before they have to ask for it.

| | What they said | What it actually is |
|---|---|---|
| 1 | “It won’t stop” | The flapper — and the in-tank bleach tablet that ate it and the seat |
| 2 | “It won’t drain” | Grease in the branch eight feet out, not the trap |
| 3 | “No hot water” | The lower element, open, in a tank full of sediment |
| 4 | “It’s dripping” | The relief valve doing its job on a closed system with no expansion tank |
| 5 | “It stinks” | A dry trap *and* a closet flange sitting under the tile |
| 6 | “It’s backing up” | Roots at a joint, forty feet out |

Each call has a shortcut that works on the day and comes back as a **callback**. Take all
six and the model bills the same **$2,845** and makes you eat **$2,675** — you worked
twice and got paid once. That number is the whole point of the level.

**The scoreboard is money, not gates:** Calls closed · Callbacks · **Ate** · Invoice.
Money you spent fixing the wrong thing moves off the invoice and into *Ate*, because you
do not get to bill a customer for your own diagnosis.

The Fixer has the same four views (**Room** and **The fault** in place of Street and Inspector) and the same zoom bar.

**Rates** — the button in the bottom bar opens every number in the deck, most expensive
first, with its source under it and the six big-ticket lines flagged. They are Treasure
Coast market ranges gathered September 2026, **not** Covenant's prices; swap your own
sub's numbers into `RATES` and every card moves with them. The sheet also carries
Indian River County's own water and sewer tiers, which is why a running toilet costs
about $670 a year here and not the $90 a homeowner expects — a leak does not get billed
at the bottom tier, it pushes the whole month into the top one.

Four of the six faults are mistakes the build model explicitly warns about: the flange
set under the tile (`ft`), the missing expansion tank (`ex`), the dry trap (`fl`), and
the cleanout at the property line that makes call 6 a four-second diagnosis (`tp`).

## The files

```
index.html            the hub
apps/pipe/index.html  the build model — one self-contained file, three.js r134 from cdnjs
apps/fix/index.html   the fixer — the same house and the same engine, six service calls
```

Everything is data. `PARTS` is the card catalog, `ND` is the drain tree node by node,
`STUBS` is every stub-up, `PARTS_W` is the floor plan. Move a fixture in `ND` and the
drain, the stub, the trap arm, the vent and the trim all move with it. There is no
`if (part.id === 'wc1')` anywhere, and there must not be.

## Sources

- Inspection codes and priority order: [Indian River County / Vero Beach 2019 BRCOM
  scheduling sheet](https://indianriver.gov/Document%20Center/Services/Planning-and-Development/Building%20Division/2019_BRCOM_Scheduling.pdf)
- Code: **FBC — Plumbing** and **FBC — Fuel Gas, 8th Ed. (2023)**. The 9th Ed. (2026)
  takes effect **31 Dec 2026**; permits issued before then are generally grandfathered.
- Licensing: **F.S. 489.113(3)** — a building contractor must subcontract plumbing
  unless he also holds that trade licence. That is card 1, and it is the reason
  CBC1253676 does not pull this permit.

Every card carries a grade: ✓ Robust (in the schedule or the code table), ~ Range
(real, but the number is on your plans), ◈ Model (simplified on purpose), ✗ Cartoon
(the common lie). **A training model, not a permit.**

## Known simplifications

- Pipe diameters are drawn 3–4× oversize so a 2 in. and a 4 in. read apart on screen.
- The fall is to scale — about 8 in. across the house — and is the thing worth looking at.
- The shell (slab, block, trusses, roof, drywall) is context, not the lesson. The whole
  shell is walked step by step in the sister repo [HOUSE](https://github.com/kingofthewisdomrealm-hub/HOUSE).

---

**Covenant Builders** · CBC1253676 · (772) 473-7115 · estimates@covenantbuilders.org
Vero Beach · Sebastian · Fort Pierce · Port St. Lucie · Fellsmere · Hablamos español
