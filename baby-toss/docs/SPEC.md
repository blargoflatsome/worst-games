# Baby Toss spec

The source of truth for what Baby Toss is and does. Rules and decisions, not code. When something is built
differently, this file changes in the same commit. Decisions and changes of mind, with reasons, are logged in
[DECISIONS.md](DECISIONS.md). Terms are defined in [GLOSSARY.md](../GLOSSARY.md).

Each rule says who decided it: **(user)** for the user's choices, **(Claude's call)** for choices made while
building that the user hasn't confirmed yet. Claude's calls are open to change; the ones that matter most are
also listed in §13 Open questions.

## 1. Overview

Baby Toss is a backyard game the user invented, made into a video game. Players stand at a set distance and toss
baby dolls into three trash cans worth different points. A short video by the user shows the real game; this spec
is the written version.

- **Name:** Baby Toss. **Tagline:** "Kid Tested, Mother Approved!" (user)
- **No opening intro** (the video's "Buermann Presents" title is not used). (user)
- One game mode, described here. A second mode, Splat! (like HORSE), is set aside for now. (user, 2026-11-27)

### How to work
- **Spec first.** Before building something new or different, say which parts of this spec change and get the
  user's OK. Update the spec in the same change as the code.
- **Don't guess.** If the spec is silent or unclear, ask, then write the answer here and log it in DECISIONS.md.
  When an open question (§13) is answered, move the answer into its section and remove the question.
- **Keep the user's words.** Names, the tagline and any joke lines stay exactly as the user wrote them.

## 2. Guiding principles
1. **The real backyard game.** Rules come from how the user plays it. The yard looks like a real backyard: grass,
   a wooden fence, a house with blue siding, trees. (user)
2. **Realistic, but the babies are dolls.** The babies move with doll physics (§5). Only in the game can they move
   and cry like they're alive, and a setting turns that off. (user)
3. **Plays anywhere.** A web page that works on a phone (iPhone Safari, Android Chrome) and a computer, with nothing
   to install. (Claude's call, agreed when the user approved the browser plan)
4. **Multiplayer first.** Several players take turns on one device. No teams. (user, 2026-11-27: teams removed)
5. **Accessible.** WCAG 2.2 Level AA for everything outside the 3D yard: labels, keyboard use, visible focus,
   contrast, 44 px tap targets, works at 320 px wide. Throwing works with the keyboard as well as by swiping (§8).

## 3. Platform and tech (Claude's call)
- One self-contained web page, `baby-toss/index.html`: HTML, CSS and JavaScript in one file, no build step.
- 3D drawing: three.js r128. Physics: cannon.js 0.6.2. Both loaded from cdnjs at those exact versions, so the
  page needs an internet connection.
- Played on the website at https://worst-games.netlify.app/baby-toss/ (user, 2026-11-27).
- Playable link: published as a private claude.ai Artifact
  (https://claude.ai/artifact/5kRh1GF6sBmgAuCuHN92g6), which the user can share. Code lives in the
  `blargoflatsome/worst-games` repo.
- **Graphics quality drops by itself** on a device that can't keep up (user, 2026-11-27): if frames average slower
  than 1/40 s over 2 s, it first lowers the resolution and softens the shadows, then removes the grass blades. It
  never steps back up during a visit, so the picture doesn't keep changing. No setting. (Claude's call: the steps
  and the threshold)
- Nothing is sent anywhere. The device remembers only the settings (§11) and the last setup (players and
  target).

## 4. The yard and the cans
- **Set throwing distance.** Every throw starts from the same spot; the view doesn't move. (user)
- Three cans stand in a rough line going away from the thrower, near the fence:

| Can | Points | Size (user) | In the game (Claude's call) |
|---|---|---|---|
| Front can | **2** | Small | Black plastic, 25 cm radius, 60 cm tall, about 5 m away, a little to the left |
| Tall can | **1** | Big and tall, easy to hit | Galvanised metal with ridges and handles, 35 cm radius, 88 cm tall, about 6.4 m away |
| Back bucket | **5** | Very small, hard to see behind the tall can | White plastic bucket with a wire handle, 19 cm radius, 40 cm tall, about 7.5 m away, partly hidden behind the tall can |

- The fence is about 9 m away and 1.9 m tall. A baby that goes over it scores 0 ("Over the fence!"). (Claude's call)
- **Looks** (user asked for more realism, 2026-11-27; details Claude's call): a sky with soft clouds that also
  lights the yard and shows in the shiny cans; a lawn with slow colour patches, clover and real swaying grass blades
  where the game is played; dog-eared cedar fence planks with knots, nails and weathering; a shingled roof, trimmed
  windows, gutter and downspout on the house; trees and shrubs with bark and leafy crowns. The cans have dark
  insides and floors, rolled rims, scuffs, grime near the base and a soft shadow on the grass; the metal can has two
  small dents.
- **The tall can is the user's real one** (user, 2026-11-27, from a photo): galvanized, with ribs near the top
  and bottom and clamp handles on the sides, and "BABY TOSS" with the logo in black vinyl on the front. The orange
  "SPLAT!" on the real can is left off. (Claude's call: the font is Fraunces at its heaviest and softest, the
  closest free match to the can's lettering; the logo is Claude's tracing of the one on the can)
- The back bucket is meant to be partly hidden: about a third of it is behind the tall can from the throwing spot.
- The cans wobble a little when hit but never tip over. (user, 2026-11-27; may be revisited) In the game only the
  can's picture rocks; the can itself stays put, so a wobble never changes whether a baby's body is in. (Claude's call)

## 5. The babies
- **Doll physics.** The babies are dolls about 39 cm long: a **stuffed, flexible body** with a **hard vinyl head,
  arms and feet**. (user) In the game (Claude's call):
  - The body bends at the waist; the stuffed legs flop from the hips; the vinyl arms swing at the shoulders; the
    head is attached at a soft neck.
  - Vinyl parts bounce and clack off the cans; the stuffed body thuds and barely bounces. So how a baby lands
    matters to whether it stays in.
- **Living babies** (setting, on by default): in the game the babies move and cry as if alive. They kick and flail,
  give a startled cry as they're tossed, open their mouths and cry when they land hard, and cry when they land in a can.
  Lying in the yard they may wriggle now and then but stay quiet. (user, 2026-11-27: no crying while idle) The baby in your hand blinks now and then, and sometimes slowly shuts its left eye and then its
  right, in a creepy way. (user, 2026-11-27; tied to Living babies because blinking is acting alive: Claude's call)
  With it off they are only dolls: no movement of their own, no crying. (user; the details are
  Claude's call)
- **Looks** (Claude's call, made more realistic at the user's request 2026-11-27): glossy vinyl skin; knit onesies
  in a few pastel colours, plain, dotted or striped, with a collar and snaps; a sculpted head (chubby cheeks, button
  nose with nostrils, lips, chin) with rosy cheeks and lips; glassy doll eyes (blue, brown, green or dark) with eyelids
  that close for blinks and crying; an open mouth while crying; painted hair with a curl; chubby arms with fingers and a
  thumb; toes on the feet (realism raised again at the user's request, 2026-11-27); a neck and waist that follow the ragdoll's
  bends. Looks only: the physics shapes and sizes are unchanged.
- **A different baby every toss** (user, 2026-11-27): each baby you pick up has a new look (onesie colour and pattern,
  hair, eyes), never the same onesie colour twice in a row, and the baby you throw is the one you were holding.
- Babies stay where they land for the rest of the turn, so later babies can hit them. The yard is cleared between
  turns. (user, 2026-11-27)

## 6. Rules
### Setup
- Players type their names. Any number from 1 to 12 (one player is practice). (user, 2026-11-27)
- **Target score:** chosen at the start; **default 20**. (user) Allowed 1 to 500, with − and + buttons in steps of 5.
  (user, 2026-11-27)

### Turns
- Each player tosses **3 babies** a turn. (user)
- Players go in the order listed. (user, 2026-11-27)

### Scoring
- A baby counts if its **body is in** a can: its body is inside the can and below its rim, even if its head, arms or
  legs stick out. (user, 2026-11-27; this replaced "fully in", where every part had to be in, after a foot that slid in
  late cost a baby its points) "The body" is the stuffed chest and belly, and the whole of each is below the rim.
  (Claude's call) A baby resting on the rim or leaning on a can scores nothing.
- A baby that stops moving with its **body in** scores right away and keeps those points, even if a later baby knocks it
  out. (user, 2026-11-27)
- A baby that stops anywhere else scores 0 for now, but can still be **late in**: if its body ends up in a can later in
  the same turn, knocked in by a later baby or wriggling in by itself (Living babies), it scores the can's points
  then, once. This can happen at any time until the turn ends, including while the player waits to toss.
  (user, 2026-11-27)
- A baby resting on another baby inside a can counts if its body is in. A pile sticking out never takes back a
  baby's points. (user, 2026-11-27)
- If several babies score at the same moment, their points are added together before checking for a bust.
  (user, 2026-11-27) "The same moment" means babies that stop within 0.35 s of each other. (Claude's call)
- A baby that goes over the fence scores 0 ("Over the fence!"), with no other penalty. (user, 2026-11-27) As it goes
  over, a shower of fireworks goes off above the fence; the 0 PTS call follows when it stops, as before. (user,
  2026-11-27; Claude's call: five rockets from behind the fence where it went over, one with reduce motion, with a
  whistle and bang when Sound is on)
- Each score shows a big call like the video: "2 PTS", "1 PT", "5 PTS", "0 PTS". (user)

### Exact target and bust
- A player must reach the target **exactly**. (user)
- **Bust:** a score that would take a player over the target busts. Their score goes back to what it was at the start
  of that turn. (user) The turn ends there, and the call shows "BUST!" with "Back to <score>". (user, 2026-11-27:
  the turn ends; Claude's call: the call's wording)
- Reaching the target exactly ends that player's turn ("Right on 20!"). (user, 2026-11-27)
- A late in counts the same way: it can bust or reach the target, and either ends the turn. (user, 2026-11-27)
- The turn ends when its last baby (and everything it set moving) stops, or earlier on a bust or reaching the target.
  (user, 2026-11-27) A baby counts as stopped after half a second still; the wait after the last baby is at most 6 s,
  so wriggling Living babies can't hold up the game. A late in shows "Late in!" under its call. (Claude's call)

### Rounds and winning
- **Everyone gets the same number of turns:** a round always finishes, even after someone reaches the target. (user)
- At the end of a round, if exactly one player is on the target, they win. If none is, play goes on to the next round.
  (user)
- **Tiebreak:** if more than one player is on the target at the end of a round, those players each take one tiebreak
  turn of 3 babies. The most points in that turn wins; there is no target or bust in a tiebreak. Late ins count as
  in any turn. If they're still tied, they take another tiebreak turn, until one leads. (user, 2026-11-27)

## 7. Grips
- Before each throw the player picks how to hold the baby. **Every grip is a valid way to throw, and each has
  different physics.** (user) The grips (user):
  1. By the **head**
  2. By the **left hand**
  3. By the **right hand**
  4. By the **left foot**
  5. By the **right foot**
  6. By **both feet** at once
  7. **Overhand**, holding the body
- The held baby hangs from the hand the way it would with that grip. (Claude's call)
- How each grip throws is Claude's first version, waiting for the user to try it and tune it:

| Grip | Feel | Arc | Power | Spin | Accuracy |
|---|---|---|---|---|---|
| Head | Steady swing | Medium | Normal | Slow tumble | Good |
| Left / right hand | Cartwheels and curves to that side | Medium | A little more | Cartwheel | Fair |
| Left / right foot | Big whip, flat and wild | Low | Most | Fast end over end | Worst |
| Both feet | High lob | Highest | Normal | Flips end over end | Fair |
| Overhand | Flat and straight | Lowest | Least | Almost none | Best |

- Each player's last grip is remembered for their next turn. (Claude's call)

## 8. Controls (Claude's call)
- **Touch or mouse: pull back to throw**, like a slingshot. (user, 2026-11-27: kept after trying it) Pick a grip from the row of buttons
  along the bottom, then press anywhere on the yard, pull down and let go. How far down sets the power: full power
  at a third of the screen height (160 to 320 px). Pulling sideways aims the other way, gently (pull left to aim
  right), so the finger never covers the cans. Power sets the square of the speed, so each can gets about the same
  stretch of the pull. The throw uses where the finger was 0.08 s before it lifted, so letting go doesn't change
  it. A ring marks where the pull started; pulling back to it cancels. While pulling, a dashed line on the grass
  points where the throw heads (a fixed length, so it never shows where the baby lands), and a power bar shows the
  power. (2026-11-27: replaced a dotted arc, which spread up the screen and hid how aiming worked) (Claude's call: the numbers)
- **When the grips don't fit** across the screen (user, 2026-11-27: couldn't reach them with a mouse): the row
  scrolls sideways by swiping, by an ordinary mouse wheel, or by clicking the half-hidden grip at the edge, which picks
  it and brings the next one into view; the edge with more grips beyond it fades out. Picking a grip by key scrolls it
  into view too.
- **Keyboard:** 1 to 7 pick a grip, Left and Right aim, Up and Down set power, Space or Enter throws.
- A hint explains swiping before the first throw of a game.

## 9. Screens
- **Setup** (the first screen): the logo beside the name, in the can's lettering font (user, 2026-11-27), the tagline, a short version of the rules, players, Play to,
  Living babies, Sound, and Start tossing. The yard shows behind it with a few babies posed mid-game.
- **Playing:** the current player, how many of their 3 babies are left, the scoreboard (every player's
  score, the round, the target, and the can values), and a settings button. On a narrow screen a long name ends
  in "…" on the scoreboard (the full name shows as the current player), and the settings button keeps its size.
  (Claude's call) A banner names whose turn it is.
- **Paused** (settings button): Living babies, Sound, Keep playing, Quit to setup.
- **Game over:** the user's Worst Human medal (their sculpture, cut out of their photo) pops in above "<name> wins!"
  (user, 2026-11-27), then the final scores, Play again (same players) and Change players. With reduce motion the
  medal just appears.

## 10. Sound (Claude's call)
- Thuds on grass, a low "bong" on the metal can (many uneven ringing pitches from about 180 Hz, a short knock; duller
  for the stuffed body, more clank for the vinyl head; the user found the first, a high ding, too cartoonish), softer thumps for the stuffed body, a short tune for points, fireworks
  whistles and bangs, and the babies' cries (Living babies only).
- **Cries vary** (user, 2026-11-27): each baby has its own voice (pitch, breathiness, wobble), and each cry is one of
  a few kinds: a short startled "waah!" when tossed; a scream or a wail with a breath in between for a hard landing;
  a wail, "wah-wah" bursts or hiccuping sobs on landing in a can. They should sound natural and like a baby across the
  yard, never harsh or close to a microphone: a voice that wavers slightly like a real one, a baby's bright vowels,
  a modest level that leaves room for the other sounds, no echo (a backyard is open air; the user found an echo
  sounded like a tunnel), and a gentle limiter on all game sounds. (user asked for
  cleaner, more baby-like cries; Claude's call: how they're made) All made by the page; no sound files.
- Sound has its own on/off setting, on by default.

## 11. Settings
- **Living babies:** on/off, on by default. (user: the setting; Claude's call: on by default)
- **Sound:** on/off, on by default. (Claude's call)
- Both are on the Setup and Paused screens and are remembered on the device.

## 12. Phase 2 (later)
- **Baby skins:** several looks for the babies, some unlocked by playing. (user, 2026-11-27) How they are unlocked
  and chosen is to be decided when Phase 2 starts.

## 12a. Not in scope (for now)
- **Splat!**, the HORSE-style mode. (user, 2026-11-27)
- Online play, accounts, high-score lists, app store versions.

## 13. Open questions
1. **Grip feel** (§7): the arc, power, spin and accuracy of each grip need the user to play and tune them.
2. **Can sizes and distances** (§4): these are guesses from the video. Does the user have real measurements?
3. **Same moment** (§6): babies that stop within 0.35 s of each other score together. Should it be longer, for
   example everything one toss knocks in?
4. **Credit:** should the game show a credit for the user as its inventor (as My Brain does in its footer)?
