# Worst Games — Claude Code rules

Backyard games made into browser games. Each game lives in its own folder (`baby-toss/`).

**Start each session with [PROGRESS.md](PROGRESS.md) only. Then read just what the task touches:**
- **`<game>/docs/SPEC.md`** (the source of truth for that game): `grep -n '^#' baby-toss/docs/SPEC.md` lists
  sections; read the ones the task covers. Screen work also reads §2 Guiding principles.
- **[docs/CODING_STANDARDS.md](docs/CODING_STANDARDS.md)** (how code is written): read §1–2 plus the sections for
  the kind of work.
- `<game>/docs/DECISIONS.md` and `<game>/GLOSSARY.md`: search them, don't read them whole.

## Key rules
- **Spec first:** propose spec changes and get the user's OK before building something new or different. Update
  the spec in the same change as the code.
- **Don't guess:** if the spec is silent or unclear, ask, then write the answer into the spec and log it at the end
  of DECISIONS.md. Never invent rules for the user's games; their facts come from the user.
- **Mark who decided:** in the spec, a rule is **(user)** or **(Claude's call)**. Claude's calls that matter go in
  the spec's Open questions.
- **Keep the user's words:** names, taglines and joke lines stay as written. Write no jokes yourself.
- **Reuse, never copy** (CODING_STANDARDS §6): use the page's existing button, switch, card or overlay before
  writing a new one.
- **Accessibility:** WCAG 2.2 AA (CODING_STANDARDS §7). Works from 320 px wide on iPhone Safari and Android Chrome.
- **Front-end HTML/CSS/browser JS:** use the `modern-web-guidance` skill first. Baseline Widely available; newer
  features only with a fallback. Keep its note in replies to a line.
- **Checking work:** no automated tests yet; check in headless Chromium at desktop and phone width (CODING_STANDARDS
  §10) and say what was and wasn't checked.
- **User-started skills only:** `/grill-with-docs` (terms to the game's `GLOSSARY.md`; decisions to its SPEC and
  DECISIONS.md, not `docs/adr/`).
- **Git:** a branch per change; merge to `main` only when the user says to.
- Update `PROGRESS.md` at the end of each session; keep it to the current state.
- Confirm before deleting or renaming a game, its pages or images.

## Commands
- Play: open `<game>/index.html` in a browser (needs internet for the libraries).
- No build, lint or test commands yet.
