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
   abilities per Category), and a delivery type. Locking it in auto-generates a name.
2. **Generated Vocations** — live view of the synergy math from the transcript: each ability grants
   1 base Vocation for its Aspect, and every *other* ability sharing its Category adds +1 bonus. Matches
   the two worked examples from the call (2 Combat abilities → 2 Flow Vocations; 4 Utility abilities →
   4 Vocations each for Cunning/Spirit/Desire/Tenacity).
3. **Confluence Board (simplified)** — select a Vocation, then place it on the pulsing "next
   available" node. Boards fill in sequence (a stand-in for the real snake-path mechanic) rather than
   anywhere you like, and placed nodes within a third get a connecting line. Board sizing per third is
   still just illustrative (sum of the two relevant Category counts). Real multi-board
   connectivity/rotation/Rune Words are intentionally not built yet — "Undo last placement" is the
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

## Editing the data (for Polak — no code experience needed beyond this)

Everything content-related lives in one clearly marked block at the top of the `<script>` tag in
`index.html`, labeled `EDIT HERE`:

- `POTENTIALS` — the hardcoded Potential pair (v0 only supports one; Step 1's picker isn't built)
- `ASPECTS` — the 8 Aspects
- `CATEGORIES` / `MAX_PER_CATEGORY` — Combat/Support/Utility and the 4-per-Category cap
- `DELIVERY_TYPES` — Single Target / Area Effect
- `ASPECT_WORDS` / `CATEGORY_WORDS` — word banks the ability-name generator picks from

Edit those, save, refresh the page. Nothing below that block needs to change for content edits.

## What's deliberately NOT here (see the Design Notes tab's "Unresolved" and "This Prototype's Scope" sections)

- Potential-pair picker / combination matrix
- Real board connectivity, rotation, multi-board snaking, Rune Words
- Vestiges / items / Orbs
- FTL meta-map, combat, run structure
- Any persistence — refreshing the page resets everything (by design, for v0)

## Next step once this feels right

Port the pure generation logic (`computeVocations`, the name generator) into whatever engine gets
picked for the real build — it's already decoupled from the DOM/UI, so that should be a clean lift
rather than a rewrite.
