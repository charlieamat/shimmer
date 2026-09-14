# Shimmer — Character Generation Prototype (v0)

Validates the ability → Vocation → Confluence Board generation logic from the Shimmer design doc
(`Game Dev Brain/Shimmer/character_system_design.md` in the vault). Nothing else — no items, no
FTL map, no combat.

## Run it

Just open `index.html` in a browser. No server, no build step, no dependencies.

## What it does

1. **Craft Abilities** — pick an Aspect (each of the 8 can only be used once), a Category (max 4
   abilities per Category), and a delivery type. Locking it in auto-generates a name.
2. **Generated Vocations** — live view of the synergy math from the transcript: each ability grants
   1 base Vocation for its Aspect, and every *other* ability sharing its Category adds +1 bonus. Matches
   the two worked examples from the call (2 Combat abilities → 2 Flow Vocations; 4 Utility abilities →
   4 Vocations each for Cunning/Spirit/Desire/Tenacity).
3. **Confluence Board (simplified)** — click a Vocation, then click an empty node to place it. Board
   is split into three "thirds" with node counts sized off Category counts, just enough to see the
   shape-changes-with-your-choices idea. Real connectivity/rotation/Rune Words are intentionally not
   built yet.

## Editing the data (for Polak — no code experience needed beyond this)

Everything content-related lives in one clearly marked block at the top of the `<script>` tag in
`index.html`, labeled `EDIT HERE`:

- `POTENTIALS` — the hardcoded Potential pair (v0 only supports one; Step 1's picker isn't built)
- `ASPECTS` — the 8 Aspects
- `CATEGORIES` / `MAX_PER_CATEGORY` — Combat/Support/Utility and the 4-per-Category cap
- `DELIVERY_TYPES` — Single Target / Area Effect
- `ASPECT_WORDS` / `CATEGORY_WORDS` — word banks the ability-name generator picks from

Edit those, save, refresh the page. Nothing below that block needs to change for content edits.

## What's deliberately NOT here (see design doc §5 and §7)

- Potential-pair picker / combination matrix
- Real board connectivity, rotation, multi-board snaking, Rune Words
- Vestiges / items / Orbs
- FTL meta-map, combat, run structure
- Any persistence — refreshing the page resets everything (by design, for v0)

## Next step once this feels right

Port the pure generation logic (`computeVocations`, the name generator) into whatever engine gets
picked for the real build — it's already decoupled from the DOM/UI, so that should be a clean lift
rather than a rewrite.
