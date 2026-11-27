# Worst Games coding standards

How code in this repo is written. A working document: edit anything here and the code follows. Each game's
`docs/SPEC.md` says *what* it does; this file says *how* the code is written. Carried over from the user's My Brain
and Catacombs of Despair rules, keeping what fits a set of small browser games.

---

## 1. Where things go

| What | Where |
|---|---|
| A game | `<game>/index.html`: one self-contained page (markup, CSS and script in one file, no build step) |
| How to play it | `<game>/README.md` |
| What it does (source of truth) | `<game>/docs/SPEC.md` |
| Decisions and changes of mind | `<game>/docs/DECISIONS.md` (newest at the bottom) |
| The game's terms | `<game>/GLOSSARY.md`, listed in `GLOSSARY-MAP.md` at the root |
| What's happening now | `PROGRESS.md` at the root |
| The website's home page (one card per game) | `index.html` at the root |
| What Netlify publishes | `netlify.toml`: copies only the playable pages into `dist` |

A game moves to several files (or a build step) only when one file gets too big to work in, and only with the
user's OK.

## 2. Inside a game page

- Order: `<title>` and font links, one `<style>`, the markup, library `<script>` tags, then one game script.
- The game script is wrapped in `{ }` so nothing leaks onto `window`, and runs top to bottom in labelled sections
  (`/* ---------- physics ---------- */`): scene, physics, the yard, the babies, controls, sound, game state, input,
  loop, screens.
- **Numbers to tune are data, not scattered literals.** Can sizes and points, body parts and joints, and grips are
  tables at the top of their section (`CANS`, `PART_DEF`, `JOINTS`, `GRIPS` in Baby Toss). Tuning means editing a
  table row.
- **Rules apart from drawing.** Scoring, bust, turn order and winning are plain functions that read and change the
  game state, never three.js objects, so they can be tested without a browser later.
- Names say what things are in the game's own words (the glossary): `cans`, `grip`, `bust`, not `targets`, `mode`,
  `reset`.
- Style: 2 spaces, single quotes, semicolons as the existing file uses them. Short helpers over long inline
  expressions.

## 3. Libraries

- Only from cdnjs (or jsdelivr / unpkg when cdnjs lacks it), pinned to an exact version, as UMD builds. This is
  also what the Artifact page allows.
- Baby Toss uses three.js r128 and cannon.js 0.6.2. No new library, and no upgrade, without the user's OK.
- Fonts from Google Fonts with a fallback face.

## 4. Wording

- Keep the user's words exactly: names, taglines and joke lines ("Kid Tested, Mother Approved!"). Write no jokes of
  your own; new wording is short and plain.
- Score calls follow the video: "2 PTS", "1 PT", "0 PTS", "BUST!".
- A game's wording lives in one place in its page (the markup for fixed text, one `MSG` table in the script for
  messages), so it's easy to change.

## 5. Comments

- Say why, not what: a reason, a rule from the spec, or a trap. If the code already says it, don't comment.
- Link rules to the spec, `(SPEC §6)`, and accessibility to WCAG, `(WCAG 2.5.7)`.
- No commented-out code, no notes about who wrote or approved something (DECISIONS.md records that).

## 6. Styling and the modern web

- Colours as tokens on `:root`; reuse them rather than one-off colours. The 3D yard keeps its own fixed colours.
- Every screen works from 320 px wide on iPhone Safari and Android Chrome, with no sideways scrolling, and on a
  computer.
- For HTML, CSS and browser JavaScript, use the `modern-web-guidance` skill first. Baseline Widely available;
  newer features only with a fallback.
- **Reuse, never copy.** Before writing a button, switch, card or overlay, use the one the page already has
  (`.card`, `.sw`, `.go`, `.sec2`, `.pill`). If it's missing, make it shared first.

## 7. Accessibility (WCAG 2.2 AA)

- Every control has a label; icon-only buttons have an `aria-label`.
- Tap targets 44 px where practical, never under 24 px.
- Anything done by swiping or dragging also works by keyboard (and a tap where it makes sense).
- Visible focus on everything; contrast checked on every overlay.
- Scores, turns and results are announced to screen readers (`aria-live`), since the 3D yard is only a picture.
- Animations of the page (not the physics) respect reduce motion.
- Windows (pause, game over) take focus when they open; Escape closes the pause window.

## 8. Privacy and safety

- Text people type (player names) is shown as text only: `textContent`, or escaped before it joins HTML.
- Nothing leaves the device. `localStorage` holds only settings and the last setup, and every read and write is
  wrapped in `try`/`catch` so the game works without it.
- Secrets: none are needed. If one ever is, it goes in `.env` (gitignored) and you ask for it first.

## 9. Performance

- Phone first: pixel ratio capped at 2, one shadow-casting light, a fixed physics step (1/240 s), and only a few
  bodies at once (the yard is cleared between turns).
- Heavy objects (textures, geometry) are made once and shared.

## 10. Checking work

- There are no automated tests yet. After a change, load the page in the pre-installed headless Chromium
  (Playwright), serving the libraries from local npm copies if the CDN is blocked, and play throws at desktop size
  and at phone width (360 px). It must show no page errors. Look at screenshots of what changed.
- When tests are added, each is named after the spec rule it proves ("bust puts the score back to the start of
  the turn").
- Before calling something done, compare it with the spec sections it covers and say which rules weren't checked.

## 11. Git and publishing

- One branch and pull request per change; merge to `main` only when the user says to.
- Commit messages: a short summary line, then what changed and why.
- The spec, README and DECISIONS.md change in the same commit as the code.
- The website, https://worst-games.netlify.app, deploys from `main` on Netlify (set up by the user: Import from
  GitHub, site name `worst-games`; build settings come from `netlify.toml`). Every merge to `main` is a
  production deploy, and the free plan allows about 20 a month, so batch changes and merge once per change.
- A new game gets a card on the root `index.html` and a line in the `netlify.toml` copy command.
- The playable Artifact link is republished from `<game>/index.html` when the user wants to try a change.
