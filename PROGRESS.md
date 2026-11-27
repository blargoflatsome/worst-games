# Progress

Current state only. Update at the end of every session.

## Baby Toss
- **Playable:** https://claude.ai/artifact/5kRh1GF6sBmgAuCuHN92g6 (private; share from the page's Share menu).
- **Built:** the yard and three cans, ragdoll babies, seven grips, exact target with bust, equal turns, tiebreak,
  optional teams, Living babies and Sound settings ([SPEC](baby-toss/docs/SPEC.md)).
- **Spec ahead of the game:** the rule clarifications of 2026-11-27 (SPEC §6) aren't built yet: remove teams,
  score late ins until the turn ends, add same-moment points before the bust check, tighten the fully-in check (a
  baby lying across the rim can count now), and let the cans wobble.
- **Next:** build those; then the user plays it and tunes the grips; the rest of the open questions (SPEC §13).

## Not yet done to the standards
- The page's wording isn't in one `MSG` table yet (CODING_STANDARDS §4); scoring, bust and turn order are mixed
  in with the drawing code (§2). Tidy both before adding features.
- No automated tests.
