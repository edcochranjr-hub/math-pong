# Math Pong — TODO

Last updated: 2026-04-30

## Live URLs
- **Production:**  https://math-pong.vercel.app/
- **GitHub repo:**  https://github.com/edcochranjr-hub/math-pong
- **Auto-deploy:**  every `git push` to `main` redeploys via Vercel (~30s)

## Current build (2026-05-01, commit a6ffedd)
Major features in place:
- Best-of-5 round structure (first to 3 round wins). Each round = first to 7 points.
- Pong bonus between rounds when 15+ correct hit during a round (winScore 3, then auto-starts next round)
- Standalone easter-egg pong (winScore 7, returns to title)
- Three difficulty modes with mode-specific magnitude caps:
  - KIDS (5-8): single-digit add/sub only, generous time
  - FAMILY (9-12): add ≤250, sub ≤99, mul ≤99 + round multipliers (5/10/25/50/100), pow with single-digit bases
  - GENIUS (13-adult): full smooth ramp through 4-digit
- Powers rule: base<5 → exp up to 4; base≥5 → exp up to 3 (caps 4^4=256, 9^3=729)
- Multi-choice mode (default ON for touch devices) + on-screen number keypad for typing mode
- Tappable everything: chevrons, MC toggle, names, START, pause, keypad, choices
- Mobile fullscreen support (auto on START tap, manual button)
- Pause overlay with RESUME / QUIT TO MAIN MENU buttons
- Pong landscape rotate prompt for portrait phone with tappable Back to menu
- Named-player phrases: MIMI loses 25% chance, TREY-vs-MIMI wins 25% chance
- Two easter eggs: decorated egg (top right of MC line) → pong; "Games By Trey" credit (bottom-left) → Mom&Dad note
- H2H records, lifetime stats, weak-spot weighting, problem-history dedup (50 deep, persistent)
- 2-minute round cap as defensive backstop

## Deferred features
- [ ] **Daily challenge mode** — seeded RNG so the same problems appear for everyone playing on a given day; persistent local "best of today" score. Adds a hook to come back daily. Self-contained chunk; can be added without touching versus/practice/custom flows.
- [x] ~~**Pong mini-game interlude**~~ — DONE 2026-04-30. Triggers once per match after 10 correct answers (versus modes only). W/S + ↑/↓ keyboard or touch (multi-touch on tablet). First-to-3, then math resumes. `PONG` constant at top of index.html for tuning.

## To verify with Trey (real-world testing)
- [ ] Round 10–12 in FAMILY mode hits low triple digits as intended
- [ ] 5 forced rounds of newly-unlocked ops feels right (currently `recentlyUnlockedRemaining = 5` in `onCorrect`)
- [ ] 25% phrase trigger rate for MIMI/TREY feels right (`PHRASE_CHANCE` near top of index.html)
- [ ] Thinking-time bonus for hard problems (algebra, powers, big mul/div) is generous enough — not punishing, not boring (`thinkingBonus()` in index.html)
- [ ] CUSTOM EXTREME (1–9999) might be too extreme for Trey at his current skill — easy to drop the ceiling in `genCustomProblem`'s `addSubMax` array
- [ ] CPU accuracy at FAMILY (86%) feels beatable but challenging
- [ ] Title screen + CUSTOM setup screen render cleanly on every browser window size you actually use (was a real bug — bottom-anchored layout should now hold up)

## Possible future additions
- [ ] More named players with custom phrases — add via `NAME_PHRASES` const near top of index.html. Schema is per-name buckets (e.g. `MIMI.lose`, `TREY.winVsMimi`); trigger logic in `setupGameOverPhrases()`.
- [ ] Mid-game pause hint (Trey may not know P pauses)
- [ ] Sound volume slider (currently just on/off via M)
- [ ] More algebra variety (substitution puzzles, simple inequalities) at high difficulty
- [ ] Per-op practice stats over time ("you've solved 47 multiplications, 23 divisions...")

## Deploy
- [ ] Push to Vercel (see README "Deploy free on Vercel" section). When live, note the URL here.

## Known quirks (not bugs, just heads-up)
- localStorage is per-device — Trey's stats / names / weak-spot history start fresh on any other browser or computer
- No git history — file mtimes on `index.html` and `README.md` are the only audit trail of when something changed
- The `mathPongV2` localStorage key — bump version if the schema ever changes incompatibly so old data doesn't crash the game
