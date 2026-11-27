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
4. **Multiplayer first.** Several players take turns on one device; teams are optional. (user)
5. **Accessible.** WCAG 2.2 Level AA for everything outside the 3D yard: labels, keyboard use, visible focus,
   contrast, 44 px tap targets, works at 320 px wide. Throwing works with the keyboard as well as by swiping (§8).

## 3. Platform and tech (Claude's call)
- One self-contained web page, `baby-toss/index.html`: HTML, CSS and JavaScript in one file, no build step.
- 3D drawing: three.js r128. Physics: cannon.js 0.6.2. Both loaded from cdnjs at those exact versions, so the
  page needs an internet connection.
- Playable link: published as a private claude.ai Artifact
  (https://claude.ai/artifact/5kRh1GF6sBmgAuCuHN92g6), which the user can share. Code lives in the
  `blargoflatsome/worst-games` repo.
- Nothing is sent anywhere. The device remembers only the settings (§11) and the last setup (players, teams,
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
- The back bucket is meant to be partly hidden: about a third of it is behind the tall can from the throwing spot.

## 5. The babies
- **Doll physics.** The babies are dolls about 39 cm long: a **stuffed, flexible body** with a **hard vinyl head,
  arms and feet**. (user) In the game (Claude's call):
  - The body bends at the waist; the stuffed legs flop from the hips; the vinyl arms swing at the shoulders; the
    head is attached at a soft neck.
  - Vinyl parts bounce and clack off the cans; the stuffed body thuds and barely bounces. So how a baby lands
    matters to whether it stays in.
- **Living babies** (setting, on by default): in the game the babies move and cry as if alive. They kick and flail,
  open their mouths and cry when they hit something hard, sometimes whimper while lying in the yard, and cry when
  they land in a can. With it off they are only dolls: no movement of their own, no crying. (user; the details are
  Claude's call)
- **Looks** (Claude's call): footed onesies in a few pastel colours, painted eyes, rosy cheeks, a little hair.
- Babies stay where they land for the rest of the turn, so later babies can hit them. The yard is cleared between
  turns. (Claude's call)

## 6. Rules
### Setup
- Players type their names. Any number from 1 to 12 (one player is practice). (Claude's call)
- **Teams are optional.** With teams on, there are two teams (Team Orange and Team Blue) and each player is put on
  one. Each team needs at least one player. A team's score is shared. (user: teams optional; two teams, names and
  sharing are Claude's call)
- **Target score:** chosen at the start; **default 20**. (user) Allowed 1 to 500, with − and + buttons in steps of 5.
  (Claude's call)

### Turns
- Each player tosses **3 babies** a turn. (user)
- Without teams, players go in the order listed. With teams, the teams alternate, and each team's players take
  turns round by round. (Claude's call)

### Scoring
- A baby counts only if it is **fully in** a can: every part of it, head, hands and feet included, is inside the can
  and below its rim. A baby resting on the rim, leaning on a can or sticking out scores nothing. (user)
- Each baby is scored once, when it stops moving, and shows a big score call like the video: "2 PTS", "1 PT",
  "5 PTS", "0 PTS". (user: the look is from the video; when it's scored is Claude's call)

### Exact target and bust
- A side must reach the target **exactly**. (user)
- **Bust:** a baby that would take a side over the target busts. Their score goes back to what it was at the start
  of that turn. (user) The turn ends there, and the call shows "BUST!" with "Back to <score>". (Claude's call: the
  turn ends)
- Reaching the target exactly ends that side's turn ("Right on 20!"). (Claude's call)

### Rounds and winning
- **Everyone gets the same number of turns:** a round always finishes, even after someone reaches the target. (user)
- At the end of a round, if exactly one side is on the target, it wins. If none is, play goes on to the next round.
  (user)
- **Tiebreak:** if more than one side is on the target at the end of a round, those sides each take one tiebreak
  turn of 3 babies. The most points in that turn wins; there is no target or bust in a tiebreak. If they're still
  tied, they take another tiebreak turn, until one leads. (user, 2026-11-27)

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
- **Touch or mouse:** pick a grip from the row of buttons along the bottom, then swipe up on the yard. A longer swipe
  throws harder; the angle of the swipe aims left or right. While swiping, a short dotted arc and a power bar show
  the start of the throw (not where it lands). A very short swipe doesn't throw.
- **Keyboard:** 1 to 7 pick a grip, Left and Right aim, Up and Down set power, Space or Enter throws.
- A hint explains swiping before the first throw of a game.

## 9. Screens
- **Setup** (the first screen): name and tagline, a short version of the rules, the Teams switch, players, Play to,
  Living babies, Sound, and Start tossing. The yard shows behind it with a few babies posed mid-game.
- **Playing:** the current player (and team), how many of their 3 babies are left, the scoreboard (every side's
  score, the round, the target, and the can values), and a settings button. A banner names whose turn it is.
- **Paused** (settings button): Living babies, Sound, Keep playing, Quit to setup.
- **Game over:** "<name> wins!", final scores, Play again (same players) and Change players.

## 10. Sound (Claude's call)
- Thuds on grass, clangs on the metal can, softer thumps for the stuffed body, a short tune for points, and the
  babies' cries (Living babies only). All made by the page; no sound files.
- Sound has its own on/off setting, on by default.

## 11. Settings
- **Living babies:** on/off, on by default. (user: the setting; Claude's call: on by default)
- **Sound:** on/off, on by default. (Claude's call)
- Both are on the Setup and Paused screens and are remembered on the device.

## 12. Not in scope (for now)
- **Splat!**, the HORSE-style mode. (user, 2026-11-27)
- Online play, accounts, high-score lists, app store versions.

## 13. Open questions
1. **Does a bust end the turn?** Built: yes, the rest of that turn's babies are lost.
2. **Can a later baby change an earlier score?** Built: no. Each baby is scored when it stops, even if a later baby
   knocks it out of a can (or into one).
3. **Grip feel** (§7): the arc, power, spin and accuracy of each grip need the user to play and tune them.
4. **Teams:** is two teams enough, and are Team Orange and Team Blue good names?
5. **Can sizes and distances** (§4): these are guesses from the video. Does the user have real measurements?
6. **Credit:** should the game show a credit for the user as its inventor (as My Brain does in its footer)?
