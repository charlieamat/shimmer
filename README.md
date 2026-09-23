# Shimmer — Character Generation Prototype (v0)

Live: https://charlieamat.github.io/shimmer/

Validates the ability → Vocation → Confluence Board generation logic behind Shimmer's character
creation. Nothing else — no items, no FTL map, no combat. Full writeup lives in the app itself under
the **Design Notes** tab.

## Run it

Open the live link above, or open `index.html` locally in a browser. No server, no build step, no
dependencies either way.

## What it does

1. **Craft Abilities** — pick an Aspect (each of the 8 can only be used once), a Category (max 4
   abilities per Category), and a delivery type. Type options are Category-specific — 4 each for
   Combat/Support/Utility. Locking it in auto-generates a name.
2. **Generated Vocations** — live view of the synergy math: each ability grants 1 base Vocation for
   its Aspect (using its own delivery type), and every *other* ability sharing its Category adds +1
   bonus Vocation, borrowing *that other ability's* delivery type — so each one is genuinely distinct,
   not a numbered duplicate. Matches the confirmed formula (22 Vocations from a full 8-ability build).
3. **Confluence (circles)** — 5 rings (2 Potentials + Combat/Support/Utility), sized by how much
   you invested in each, arranged in a fixed pentagon so each only overlaps its two neighbors.
   Rotate a ring (◀ ▶) to change which of its nodes lines up at its two intersections. Select a
   Vocation, then click a lit-up intersection to place it. **Replaces the old triangular-path
   board entirely** — see the "Confluence rebuild" section below.

### Design pass (2026-09-14)

A few things were added specifically to make the *mechanic* readable, not just the data:
- **Category fill meters** at the top of the builder panel, so investing 4 abilities into one Category
  is visibly filling a bar, not just incrementing a number.
- **Boost flash** — when a new ability bumps other Vocations in its Category, those rows briefly
  highlight, so the retroactive-synergy moment is visible in the moment it happens.
- **Hover-linking** — hovering an ability card highlights the Vocation row(s) it produced, and vice
  versa isn't built (rows aren't hoverable back) but the ability→Vocation causality is traceable.
- **Aspect chip tracker** — all 8 Aspects are always visible; used ones go greyed + struck-through
  instead of disappearing, so "each Aspect once" is obvious at a glance.
- **Category shape icons** (▲ Combat, ● Support, ◆ Utility) — pulls Polak's own Figma shape language
  (arrow/circle/diamond) into the prototype instead of using generic UI.

### Board rewrite (2026-09-16)

Item 7 from the round-2 feedback — the board is now a real per-path progression instead of "center
unlocks everything":
- **Progressive unlock:** each path's nodes fill in strict order (center → node 1 → 2 → 3 → the
  4th-node Vocation slot). Regular nodes just need a click; Vocation nodes still need a Vocation
  selected first.
- **Remove any node:** click any filled node (not just the most recent one) to remove it. "Undo
  last placement" still works too, as a quick-undo shortcut.
- **Broken-chain detection:** removing an earlier node that orphans nodes further down the path
  marks those nodes as broken (red, dashed, "!"), with a warning and a one-click "Clear broken
  nodes" button to clean them up.

In-game framing (per Polak's Discord writeup): this mirrors "you spend points along a path to
reach an exit node" — the regular nodes represent real investment, not just visual connectors, so
pulling one out is meant to have a consequence. Open question for Polak: does removing a board
placement cost anything in the real design (like the resource-cost Orb extraction on Vestiges), or
is it meant to be free? The prototype currently treats it as free.

### Potential picker + "finished Perpetual" completion (2026-09-17)

First step toward the "login to finished Perpetual" scope Polak asked for:
- **Added:** a real Step 0 — pick exactly 2 Potentials from a list to form your pair, then lock it
  in. Everything else (ability crafting, Vocations, the board) stays hidden until you do. The 4
  Potential names in the list right now (`POTENTIAL_OPTIONS`) are placeholders — swap in the real
  list whenever it's ready, the picker works with however many are in there.
- **Added:** a "change" link next to the locked Potentials that resets everything (with a confirm)
  so you can try a different pair without reloading the page.
- **Added:** a "Perpetual Complete" banner once all 8 Abilities are crafted, summarizing the
  Potentials pair, ability count, total Vocations generated, and how many of the board's 4 Vocation
  nodes are filled — a visible, testable endpoint for the whole creation flow, which is the actual
  point of this pass. Doesn't lock the UI afterward; everything stays editable/inspectable.
- Not addressed yet: this only closes the mechanical loop. Polak's cohesion/bloat/creep concern about
  Potentials thematically working across every ability is a content question, not a UI one — it'll
  become visible once real Potential names and more Aspect-effect content exist (see vault §18, §23).

### QA pass on the Confluence rebuild (2026-09-23)

Polak's first real playtest of both Confluence modes, via DM (a batch of 11 numbered items). Nine
were concrete and are fixed; two are flagged for discussion rather than guessed at — see the vault
design doc §31 for the full breakdown. Also: he confirmed the board's biggest open question from the
last round — **intersections as Vocation slots is the right read**, no longer just our assumption.

Fixed:
- **Vocation naming:** now shows the ability's own single Potential (e.g. "Earth"), not the blended
  "Earth+Honor" left over from before the Potential split.
- **Bonus Vocation numbering:** reads Base, 2, 3, 4 (Base occupies "1" conceptually) instead of
  Base, 1, 2, 3.
- **Ring investment text:** high-contrast with a background-colored halo instead of dim text in the
  ring's own (sometimes hard-to-read) color.
- **Removed the placeholder node-label line** at the bottom of the Confluence panel — cosmetic
  filler text that read as gibberish, not real information.
- **Empty rings no longer render:** a 4/4/0 Category split (or any 0-count Category) now shows only
  the active rings — no more a 5th ring for a Category with nothing in it. Applies to both modes.
- **"X/Y intersections filled" counter** added directly in the Confluence panel, matching the
  existing 0/4 (Types) and 0/5 (Potentials) style elsewhere.
- **Rings can no longer float alone in drag mode:** a move is blocked if it would leave any ring
  (the one moved, or one displaced in a swap) with zero occupied neighbors — with the pentagon/hex
  layouts here, that's reachable mainly with very few abilities crafted (sparse rings + a center
  move), and is blocked with an inline warning rather than silently allowed.
- **New ability-creation rule:** a (Category, Type) combo can be used at most once per Potential —
  so at most 2 total across both Potentials, never the same Potential twice for the same combo. The
  Type dropdown disables the option once it's taken for whichever Potential is currently selected.

Flagged, not guessed at (see vault §31 for the full write-up):
- **What happens when a drag-mode move unslots a placed Vocation** — right now it's silently
  dropped ("sacrificed"); Polak's own message floated "moving out of the way" as an alternative.
  Needs his call before building either direction.
- **One item (his #6) we couldn't map to anything in the current build** — asked for clarification
  rather than guessing which text he meant.

Also delivered, not wired into the live prototype (exploratory, per his ask): three static sketches
of concentric-ring layout options for representing more node-capacity without growing ring size —
`concentric-ring-concepts.html`.

### Confluence: second interaction mode, drag/arrange (2026-09-21)

Charlie's own re-read of Polak's ring description after seeing the first build: "It's more that
the rings get dragged and snapped into place over each other and that is what consumes or creates
the slots (I think)." That's a materially different mechanic from the rotate-in-place version
below — so rather than guess and rebuild over it, **both are now live side by side** via a mode
toggle at the top of the Confluence panel, so Polak can compare and pick:

- **Rotate rings (fixed pentagon)** — the original build (see below): rings sit in fixed spots,
  rotating one changes which of its nodes lines up at its two fixed intersections.
- **Drag rings (rearrange), new:** the 5 rings sit among 7 fixed anchor spots (1 center + 6 around
  it, hex-flower layout, 2 spots always open for maneuvering room). Click a ring to pick it up,
  click an anchor to drop it there (swapping if occupied). An intersection only exists where two
  *adjacent* anchors both currently hold a ring — moving a ring is literally what creates or
  destroys that slot, matching Charlie's "consumes or creates" phrasing. No rotation control in
  this mode; position is the whole mechanic. Moving a ring out of an intersection auto-clears any
  Vocation placed there, same "consequence for pulling out" feel as the old board's broken-chain
  behavior.
- **Still the same open question as below:** where Vocations attach is a working assumption either
  way (intersections = placeable slots), not confirmed by Polak.
- Verified via Playwright: toggle switches cleanly between modes without disturbing the other's
  state; rings can be picked up and moved/swapped between anchors; intersections activate only when
  both adjacent anchors are occupied; moving a ring away from an intersection prunes any Vocation
  placed there; the original rotate mode still works unchanged after switching back to it.

### Confluence rebuild + Potential split (2026-09-21)

Two pieces, both from Polak's latest round (design doc §25-27):

- **Potential-per-ability split, built:** each Ability now gets tagged with one of the 2 locked
  Potentials, with a minimum of 3 per side enforced (splits: 3/5, 4/4, or 5/3) — no more forcing
  every ability to blend both Potentials into one theme. The exact "3" minimum wasn't restated
  numerically in the latest message; carried forward from the earlier proposal as the working
  number, flagged as an assumption.
- **Confluence (circles), built — full replacement of the triangular board:** 5 rings (2
  Potentials + Combat/Support/Utility), each sized by investment count, arranged in a fixed
  pentagon so overlaps stay strictly pairwise (no 3+-way overlaps — his own stated preference).
  Each ring's nodes are evenly spaced; rotating a ring snaps between fixed positions, changing
  which node lines up at each of its two intersections with its neighbors — the "reveal by
  sliding" mechanic from his Discord message.
- **Open, not guessed at:** where Vocations actually go on the new board wasn't spec'd. The
  prototype's working assumption is that the 5 intersections are the placeable slots (same role
  the old board's Vocation nodes played) — flagged in the Design Notes tab and worth an explicit
  confirm from Polak rather than treating as settled.
- Not built: Rune Word circles (adds more rings later) — same "out of scope for v0" as
  multi-board was before.
- Verified via Playwright: 5 rings/5 wedges render, ring sizes track ability counts exactly,
  rotating a ring changes the revealed node text, Vocation placement/removal on intersections
  works, completion banner reflects the new "X/5 intersections" wording, Potential-split cap
  (max 5 per side) correctly blocks over-committing to one Potential.

### Feedback pass 4 (2026-09-16)

Polak's follow-up on the field-order fix — a concrete, buildable UI idea:
- **Reordered:** Category + Type now come first in the Craft Ability panel, Aspect selection moved
  below them. Matches his stated flow: lock in the "what kind of ability" part first, then explore
  Aspects against it.
- **Added:** two live preview boxes. The first shows a stable "ability so far" name generated from
  Category+Type alone, as soon as both are picked. The second appears once you click an Aspect and
  shows how that Aspect changes the ability — clicking a different Aspect updates it live, so you
  can cycle through all 8 and compare, per his HoJ+Cunning / HoJ+Will example. Both preview names are
  deterministic (not re-rolled every render), so they hold still while you're comparing.
- **Note:** these previews are still just placeholder *names*, not real mechanical effects — the
  actual effect-matrix idea from his HoJ example (each Aspect being a defined stat/ability modifier)
  is still the bigger, unscoped item flagged in the vault design doc §18.

### Feedback pass 3 (2026-09-16)

Polak's answers on round-2 open items, one concrete fix built (the rest are confirmations or
deferred — see the vault design doc §16 for the full breakdown):
- **Fixed:** Aspect, Category, and Type in the Craft Ability panel can now be picked in any order.
  Previously the panel was fully stateless per-render, so picking one field silently reset the
  others. Now all three persist independently — you can pick Category → Type first, then cycle
  through Aspects, per Polak's request — and only Type resets, and only when Category itself
  changes (since its options depend on Category).
- **Confirmed, no change needed:** the board's pull-Vocation-and-replace repair loop already works
  as described — removing a Vocation breaks the path, placing a different one in the same V-Node
  repairs it, purely because chain validity is presence-based per node.
- **Confirmed:** board removal stays free for now (could change later).
- **Still open:** border node count isn't finalized — Polak's citing Path of Exile / WoW
  Dragonflight talent trees as inspiration; regular nodes = stat increases, Vocation nodes = talents.
- **Noted, not building yet:** an idea for Vocation nodes buffing (or being buffed by) nearby
  regular nodes on the board — explicitly flagged by Polak as an unspec'd future balance variable,
  not a decision.
- **Still deferred:** the alternate ability-creation grid view and cross-page filtering — Polak
  wants to talk those through live before anything gets built.

### Feedback pass 2 (2026-09-16)

Polak's second round on the fixed build, items 1-4 addressed (5-8 need discussion first — see the
vault design doc §14 for the full breakdown):
- **Added:** a remove (✕) button per crafted Ability, not just "Reset everything."
- **Fixed:** the Type dropdown now stays disabled until a Category is explicitly chosen, instead of
  defaulting to one.
- **Fixed:** bonus Vocations now show a cumulative level (base, +1, +2, +3...) instead of every bonus
  reading a flat "+1."
- **Added:** Category color-coding (Combat/Support/Utility) on Vocation rows, the pool picker, and
  filled board nodes, so synergizing Vocations are visually grouped.

### Feedback pass (2026-09-15)

Polak's first reaction to the live build surfaced two bugs and a vague board complaint, all now
addressed:
- **Fixed:** the delivery-type dropdown now shows the correct 4 Category-specific options instead of
  a generic Single Target/AOE pair for all three.
- **Fixed:** duplicate Vocations for the same Aspect are now genuinely distinct (different delivery
  type each), not numbered stacks of the same name.
- **Rebuilt:** the board is now the actual spec Polak described — center + 3 triangle paths, real
  Vocation nodes only at the center and each path's 4th node. His "1-2 extra nodes at the border" note
  is implemented as 2 regular (non-Vocation) offshoot nodes — flagged in the Design Notes tab as an
  assumption pending his confirm, since his phrasing there was a little loose.

## Editing the data (for Polak — no code experience needed beyond this)

Everything content-related lives in one clearly marked block at the top of the `<script>` tag in
`index.html`, labeled `EDIT HERE`:

- `POTENTIALS` — the hardcoded Potential pair (v0 only supports one; Step 1's picker isn't built)
- `ASPECTS` — the 8 Aspects
- `CATEGORIES` / `MAX_PER_CATEGORY` — Combat/Support/Utility and the 4-per-Category cap
- `DELIVERY_TYPES` — the 4 delivery types per Category (object keyed by Category name)
- `ASPECT_WORDS` / `CATEGORY_WORDS` — word banks the ability-name generator picks from

Edit those, save, refresh the page. Nothing below that block needs to change for content edits.
(The board's layout constants — `RADIUS_STEP`, `BORDER_EXTRA` — sit just below that block if the
triangle's size or border-node count need tweaking; they're visual/layout knobs, not content.)

## What's deliberately NOT here (see the Design Notes tab's "Unresolved" and "This Prototype's Scope" sections)

- Potential-pair picker / combination matrix
- Real board connectivity, rotation, multi-board snaking, Rune Words
- Confirmation on what the border "1-2 nodes over" actually are (Vocation nodes or not) — current
  build guesses "not," pending Polak
- Vestiges / items / Orbs
- FTL meta-map, combat, run structure
- Any persistence — refreshing the page resets everything (by design, for v0)

## Next step once this feels right

Port the pure generation logic (`computeVocations`, the name generator) into whatever engine gets
picked for the real build — it's already decoupled from the DOM/UI, so that should be a clean lift
rather than a rewrite.
