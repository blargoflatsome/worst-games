# Progress

Current state only. Update at the end of every session.

## Website
- **https://worst-games.netlify.app** (from `main`): a home page with a Play link to each game. Merged
  2026-11-27. **The user still needs to create the Netlify site** (Import from GitHub →
  `blargoflatsome/worst-games`, branch `main`, site name `worst-games`; `netlify.toml` has the build settings).

## Tutorial
- The root `README.md` is a tutorial on how Baby Toss was made, with a small working example game in
  `tutorial/starter.html` (not published on the website). On `ccr-2b3b8f7f-hy09hk`, not merged.

## Baby Toss
- **Playable:** https://claude.ai/artifact/5kRh1GF6sBmgAuCuHN92g6 (private; share from the page's Share menu).
- **Built:** everything in the [SPEC](baby-toss/docs/SPEC.md): the yard and three wobbling cans, ragdoll babies,
  seven grips, exact target with bust, late ins, same-moment points, equal turns, tiebreak, 1–12 players (no
  teams), Living babies and Sound settings. Merged to `main` 2026-11-27.
- Throwing is pull back like a slingshot (SPEC §8), merged 2026-11-27.
- More realistic yard, light, babies and cans, with automatic quality drop (SPEC §3–5), merged 2026-11-27.
- Fireworks over the fence, blinking held baby, scoreboard fixed at 320 px, merged 2026-11-27.
- Tall can lettered like the user's real can; logo and can font on the home screen, merged 2026-11-27.
- Worst Human medal on the winner screen, merged 2026-11-27. (The throne was tried and removed.)
- **Body in** replaces fully in: a baby counts if its body is in the can, head, arms and legs may stick out
  (SPEC §6). Fixes a foot that slid in late costing a baby its points. Merged 2026-11-27.
- Sculpted feet instead of balloons (SPEC §5), merged 2026-11-27.
- The user's own logo (from their Silhouette file, without the B) on the home screen, tall can and website,
  merged 2026-11-27.
- Setup rules in the user's words; winner screen shows the name and "You are the #1 tosser!", merged 2026-11-27.
- **Phase 1 is complete** (user, 2026-11-27).
- **Phase 2 (later):** multiple and unlockable baby skins (SPEC §12).
- **Next:** the user plays it and tunes the grips; the open questions (SPEC §13), including how long "the same
  moment" is.

## Not yet done to the standards
- Setup screen labels are still in the markup (fine by §4); the script's messages are in `MSG`.
- No automated tests.
