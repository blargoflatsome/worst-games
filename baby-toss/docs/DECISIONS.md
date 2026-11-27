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
