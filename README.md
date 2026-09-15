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
