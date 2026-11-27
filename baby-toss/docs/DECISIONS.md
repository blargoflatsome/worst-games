# Decisions

Significant decisions and changes of mind for Baby Toss, with the reason, newest at the bottom. Add new decisions at
the end.

- 2026-11-27: Build it as a browser game first (one web page, phone and computer, nothing to install), published as
  a private claude.ai Artifact; app store versions only later, if the game is fun. User approved the plan.
- 2026-11-27: Can values and sizes from the user: front can small, 2 points; middle can big and tall and easy,
  1 point; back can very small and hard to see behind the middle can, 5 points. A baby must be fully in to count.
  3 babies a turn. Target chosen at the start, default 20. Set throwing distance, multiplayer, teams optional. (user)
- 2026-11-27: Look: realistic, but the babies stay dolls with doll physics. In the game they can move and cry like
  they're alive, with a setting to turn that off. Name "Baby Toss", tagline "Kid Tested, Mother Approved!", no
  opening intro. (user)
- 2026-11-27: Tech (Claude's call): three.js r128 and cannon.js 0.6.2 from cdnjs, chosen because the Artifact page
  only loads pinned UMD builds from a few CDNs; both are old but stable, and swapping them is a big job.
- 2026-11-27: Win at exactly the target, and everyone gets the same number of turns. (user) The first version had
  the game end the moment someone reached the target; changed so the round always finishes.
- 2026-11-27: Babies are stuffed and flexible in the body with hard vinyl head, arms and feet; seven grips (head,
  each hand, each foot, both feet, overhand by the body), each valid with different physics. (user) Built the baby
  as a ragdoll of seven parts with soft joints, replacing the first version's one stiff piece. How each grip throws
  (arc, power, spin, accuracy, curve) is Claude's first version, for the user to tune.
- 2026-11-27: Going over the target is a **bust**: the score goes back to the start of that turn. (user) Replaces
  Claude's first version, where the throw just didn't count. Claude's call: a bust also ends the turn (open
  question in SPEC §13).
- 2026-11-27: Ties at the target are settled by tiebreak turns, most points wins, repeated until one leads. (user
  confirmed Claude's proposal)
- 2026-11-27: Splat! (the HORSE-style mode) is set aside; focus on Baby Toss. (user)
- 2026-11-27: Project rules carried over from the user's other repos (My Brain, Catacombs of Despair): spec first,
  don't guess, decisions log, glossary, accessibility, reuse, modern web, branch per change. (user asked; Claude
  picked which rules fit)
- 2026-11-27: Rule clarifications (user, in a grilling session). Teams removed to keep it simple: players go in the
  order listed. A bust ends the turn, and so does reaching the target exactly. A baby that stops fully in keeps its
  points even if knocked out later. A baby that stops anywhere else can still be **late in**, knocked in by a later
  baby or wriggling in by itself, at any time until the turn ends (waiting is allowed); it scores then, once. Points
  that land at the same moment are added together before the bust check. Stacked babies count if fully in. Over
  the fence is just 0. Babies stay where they land until the turn ends. Cans wobble but never tip (may revisit).
  1–12 players; target 1–500 in steps of 5. Replaces Claude's first version, where each baby was scored only once,
  when it stopped.
- 2026-11-27: Built the rule clarifications. Teams are gone from the page. Each baby has a state (flying, in, out,
  gone) and the yard is watched until the turn ends, so late ins score whenever they happen, even while the player
  aims. Fully in now uses each part's whole size (before, half of it could poke over the rim). Cans wobble in the
  picture only. Claude's calls: "same moment" is 0.35 s (open question in SPEC §13), stopped is 0.5 s still, the
  wait for the last baby is capped at 6 s, and a late in's call says "Late in!". Also moved the script's messages
  into one `MSG` table and the scoring and turn order into plain functions (CODING_STANDARDS §2, §4).
- 2026-11-27: Swipe controls reworked after the user played on a Samsung phone: the swipe area felt small, the front
  can was hard to aim for, and releasing often threw short or far. A swipe can now start on the grip buttons (the
  bottom of the screen, where the thumb starts), full power needs a longer swipe and power is spread evenly across
  the cans (front to tall can went from about 40 px to about 90 px of finger travel), aim follows sideways
  movement instead of the swipe's angle, and the last 0.08 s before the finger lifts is ignored. (user agreed to
  Claude's proposal)
- 2026-11-27: Throwing changed from swiping up to pulling back like a slingshot. (user) Full power had needed a
  swipe of 70% of the screen. Claude looked at similar games (slingshot, darts, golf, cornhole): pull-back is the
  common one-gesture design, needs only a short pull and keeps the finger off the target; timed power meters are
  hard to hit on touch. Full power is now a third of the screen. If it doesn't feel good, a backswing and toss
  (slide down for power, flick up to throw) is next (SPEC §13).
- 2026-11-27: The aim guide is now a dashed line on the grass pointing where the throw heads, plus the power bar.
  The dotted arc it replaces spread up the screen near the camera, so the user couldn't tell how aiming worked and
  thought pressing over a can should aim at it. The line has a fixed length, so it still never shows the landing
  spot. (user asked for clearer aiming; Claude's design)
- 2026-11-27: Pull back with the aim line on the grass feels good; the backswing-and-toss fallback is dropped. (user)
- 2026-11-27: More realistic graphics for the yard and light, the babies and the cans (user picked those three, not
  camera effects); quality drops by itself on slow devices, with no setting (user). Built with the three.js r128
  already loaded, no add-on files: the sky dome is also the light and reflection source; grass blades are one
  instanced mesh that doesn't write depth (drawn far to near) so the aim line stays visible over it. Colours picked
  by eye are now converted to linear, which fixed babies looking washed out. A frame slower than 0.5 s was first
  ignored as a tab switch, which would have stopped a very slow phone ever dropping detail; now only the first
  frame back from a hidden tab is ignored.
- 2026-11-27: Over the fence sets off fireworks before the 0 PTS call (user). They burst low over the fence, at 2.9
  to 4.4 m, so they show on a phone screen and in front of the trees; the first try burst too high and too soft to
  see. Only the foot grips clear the fence at full power in the current tuning (open question 1, grip feel).
- 2026-11-27: The held baby blinks, sometimes one eye then the other (user); tied to Living babies (Claude's call,
  following SPEC §2: acting alive is what that setting turns off).
- 2026-11-27: Scoreboard fixed at 320 px: the settings button was squeezed to 22 px and long names ran into the
  scores. Long names now end in "…" there.
- 2026-11-27: The tall can now looks like the user's real can from their photo: galvanized, "BABY TOSS" and the logo
  in black vinyl (not shiny), no orange "SPLAT!" (user). The lettering looks like Cooper Black, which isn't free for
  the web; Fraunces 900 with its softness axis at 100 is the closest Google Font (Claude's call). The logo is Claude's
  vector tracing (a baby diving into a wheelie bin with its lid open), kept once in the page's markup and drawn on the
  can from the same paths; the user can supply the original to replace it. The home screen shows the logo and uses
  the font for the name.
- 2026-11-27: Phase 2 added to the spec: multiple baby skins, some unlockable (user).
- 2026-11-27: On the home screen the logo's baby is drawn smaller and further from the bin, which reads better at
  logo size; the can keeps the logo as drawn, which the user liked. (user)
