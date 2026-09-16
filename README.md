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
3. **Confluence Board (simplified)** — a shared center node plus 3 paths radiating out to trace an
   invisible equilateral triangle, weighted Combat+Utility / Combat+Support / Support+Utility. The
   center and each path's 4th node (marked ★) are real Vocation nodes; everything else is a passive
   node you snake past. Select a Vocation, then place it on a pulsing ★. "Undo last placement" is the
   escape hatch instead of free node removal.

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
