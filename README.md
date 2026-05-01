# Math Pong

A family math game. Solve a math problem to "hit" the ball — the faster you answer, the less time your opponent gets to answer theirs.

## Play locally

Just open `index.html` in any modern browser. No build step, no install.

## Input mode (toggle with C on title)

Independent of which game mode you pick, you can play with either:

- **TYPE** *(default)* — type the answer with number keys, auto-submits when correct, ENTER force-submits.
- **MULTIPLE CHOICE** — every problem comes with **4 numbered choices** in a 2×2 grid. Press **1, 2, 3, or 4** to pick. Distractors are plausible-but-wrong (off-by-1, off-by-10, adjacent products, sign flips) so they don't give the answer away.

Multi-choice composes with every game mode: 2 PLAYERS, 1 vs CPU (CPU picks correctly per its accuracy %, picks a wrong choice on a miss), PRACTICE, or CUSTOM. Setting persists per browser.

## Game modes (pick on title screen with ▲ / ▼)

- **2 PLAYERS** — same keyboard, taking turns. First to 7.
- **1 PLAYER vs CPU** — solo play. CPU's speed and accuracy scale with the difficulty mode.
- **PRACTICE MODE** — drill one operation (or mixed, including algebra). No score, no opponent, just rapid problems with a streak counter and the same time-pressure curve. Press SPACE on the title to enter the operation picker, then ESC anytime to quit.
- **CUSTOM** — pick *exactly* which operations are in the pool (any combination of `+ − × ÷ ^` and algebra) and how big the numbers get. No auto-leveling — the difficulty knob is fixed at what you set. Press SPACE on the title to open setup; toggle ops with **1–6**, adjust difficulty with **◀ ▶**, **SPACE** to start, **ESC** to back.

## Controls

### Title screen
- **◀ ▶** — change difficulty (KIDS / FAMILY / GENIUS)
- **▲ ▼** — change mode (2 PLAYERS / 1P vs CPU / PRACTICE)
- **N** — rename players (saved to this browser)
- **Space / Enter** — start

### During a game
- **Number keys (0–9)** — type your answer (auto-submits when it matches). In multi-choice mode, **1–4** picks a choice.
- **Enter** — force-submit
- **Backspace** — delete a digit
- **`-`** — start a negative number (only at higher levels)
- **P** — pause / resume
- **Q** *(while paused)* — quit to main menu (so you can change mode, names, or difficulty)
- **M** — toggle sound (anywhere)
- **Esc** — (practice / custom mode) quit back to title; (paused) also quits to menu

Player 1 is the left paddle. Player 2 (or CPU) is the right paddle. Only the player whose turn it is types — the keyboard is shared.

## Difficulty modes (pick on title screen with ◀ / ▶)

| Mode | Ages | Max time | Min time | Max drop/rally | Caps at | Ramp |
|---|---|---|---|---|---|---|
| **KIDS** | 6–9 | 6.0s | 3.0s | 0.5s | Times tables (level 4) | 2.2× slower |
| **FAMILY** *(default)* | 9–13 | 5.0s | 2.0s | 0.8s | Powers (level 8) | 1.4× slower |
| **GENIUS** | 13+ | 5.0s | 1.5s | 1.2s | Negatives (level 9) | 1.0× (full) |

## How the speed works

- First problem: the mode's **max time** (KIDS 6s, FAMILY/GENIUS 5s).
- The next player's time limit = **your time + 1 second** buffer, but never below…
  - the mode's **min time**, OR
  - the **typing floor** based on how many digits the answer has:
    1 digit → 1.5s, 2 → 1.9s, 3 → 2.3s, 4 → 2.7s, 5 → 3.1s, 6 → 3.5s.
- A single rally can't shrink the limit by more than the mode's **max drop** — pressure ramps gradually instead of collapsing in one fast answer.
- Miss or time-out → opponent scores, loser serves the next round at the mode's max time.
- First to **7 points** wins.

So a 4-digit answer always gets at least ~2.7s no matter how fast the prior player went, and KIDS mode never drops below 3.0s.

### Thinking-time bonus for hard problems

On top of the typing floor, complex problems get an extra "thinking bonus" added to their time limit (and to the floor) so mental work isn't punished:

| Problem type | Bonus |
|---|---|
| Powers (`9^4`) — answer > 1000 | +2.0s |
| Powers — answer > 100 | +1.5s |
| Powers — small | +0.8s |
| Algebra two-step (`2x + 3 = 11`) | +2.0s |
| Algebra `ax = c` | +1.0s |
| Algebra `a + x = c` / `a − x = c` | +0.6s |
| `× ÷` with big answers (>500 / >50) | +1.0s / +0.4s |
| `+ −` with big answers (>5000 / >500) | +0.6s / +0.3s |
| Anything else | 0 |

This is tuned for "smart kid doing it in their head." Not savant pace, but enough that algebra and powers don't feel rushed.

## Paddle behavior

Paddles aren't static. They idle-bob while waiting, the active solver's paddle parks near the ball, the receiver chases the ball during travel, and a paddle that just whiffed wiggles in frustration. On a correct answer the paddle snaps to wherever the ball is going to be hit.

## Difficulty curve (auto-advances after correct answers — versus modes only)

The level number ramps with total correct answers, scaled by mode (KIDS ramps 2.2× slower, FAMILY 1.4×, GENIUS 1.0×). When a new operation unlocks, the **next 5 problems are forced to use that op** so you actually get to practice what you just earned — instead of the labels saying "Times Tables" while the random picker keeps giving you addition.

| Level | After ~N correct (FAMILY) | Pool         | New |
|---|---|---|---|
| 1 | start    | `+`             | start |
| 2 | 6        | `+ −`           | **− subtraction** |
| 3 | 11       | `+ −`           | bigger numbers |
| 4 | 20       | `+ − ×`         | **× times tables** |
| 5 | 28       | `+ − ×`         | bigger × |
| 6 | 36       | `+ − × ÷`       | **÷ division** |
| 7 | 47       | `+ − × ÷`       | big numbers |
| 8 | 59       | `+ − × ÷ ^`     | **^ powers** |
| 9 | 70       | `+ − × ÷ ^ ±`   | **negatives mixed in** |

KIDS mode caps at level 4 and never advances past times tables. GENIUS hits level 9 fastest. The persistent HUD label shows the actual op symbols available (e.g. `+ − ×`), not a marketing name.

### Number magnitude ramp (within an op)

Tier controls *which ops* are in the pool. A separate magnitude curve controls *how big the numbers are* — both sides face the same range at any given moment, so you don't get one side seeing `4+1` while the other gets `87+92`. The ramp is keyed to round number (scaled by mode's tierMult), not pure randomness within a wide range.

For add/sub in **FAMILY** mode (other modes scale via tierMult):

| Round | Range |
|---|---|
| 1–3 | 1–9 (pure single digit) |
| 4–5 | 3–15 |
| 6–8 | 8–30 (low doubles) |
| 9–11 | 20–99 (high doubles) |
| **12–15** | **60–300 (low triples)** |
| 16–21 | 200–999 (full triples) |
| 22+ | 500–9999 |

There's a **3% chance of a "variety bump"** in early rounds — drops in a slightly harder problem for spice, prevents single-digit monotony.

Newly-unlocked ops (e.g. `×` when it first appears at level 4) start at low complexity (capped at level 5) for their first 5 forced rounds, so you don't go from `4+1` straight to `47×8`.

`mul` / `div` / `pow` ranges scale similarly. Powers stay at base 2–5 through round 12, base 2–9 only after round 16.

## Custom mode difficulty knob

In CUSTOM, you set the number magnitudes directly. No auto-leveling — what you pick is what you get every round.

| Level | Label | Numbers |
|---|---|---|
| 1 | EASY | 1–9 |
| 2 | BASIC | up to 25 |
| 3 | STANDARD | up to 99 |
| 4 | HARD | up to 500 |
| 5 | EXTREME | up to 9999 |

## Algebra (in PRACTICE and CUSTOM)

Selecting algebra gives you linear equations to solve for `x`. The complexity scales with difficulty:

- **EASY/BASIC** — `a + x = c`, `a − x = c` (find x given the equation)
- **STANDARD** — adds `ax = c`
- **HARD/EXTREME** — adds two-step equations `ax + b = c` and `ax − b = c`

Type the value of `x`. The display omits the `= ?` since the equation is already complete.

## Streak rewards

Get 5 correct in a row and the screen erupts: **lightning, volcano, tornado, meteor shower, or aurora**. Every additional 5-streak triggers another effect.

## Easter egg (for Trey)

A small decorated easter egg sits in the **bottom-right corner of the title screen** — pulsing softly. Click it (or press **E**) to reveal a personal message, then **SPACE** drops into a standalone Pong game (first to **7**, no math wrapping it). **ESC** anywhere backs out.

The message and trigger live in the `EASTER_MESSAGE` constant near the top of `index.html`. Edit freely.

## Pong bonus round

Hit a **15-correct streak** during a versus match and you unlock a **bonus Pong round** that plays *after* the math match ends. Doesn't interrupt the game. First to **3 points** wins, then back to title. (The earlier "interlude" version that fired mid-match has been removed — pong only plays after the math match completes.)

Controls:
- **Left paddle:** `W` (up) / `S` (down)
- **Right paddle:** `↑` (up) / `↓` (down)
- **Touch (phone/tablet):** drag your paddle by touching anywhere on your half of the screen. Multi-touch — both players can drag simultaneously.
- **Esc:** quit the whole match back to title

Ball accelerates with each paddle hit and angles based on where it strikes the paddle. Skipped in PRACTICE and CUSTOM modes (no match concept). Tunable via the `PONG` constant near the top of `index.html` (`TRIGGER`, `WIN_SCORE`, paddle/ball speeds, etc.).

## Learning aids

- **Show the answer on miss** — when you get one wrong or run out of time, the equation appears with the correct answer (e.g. `7 × 8 = 56`) and stays on screen long enough to read. Failure becomes a flashcard.
- **Off-by hints** — if you typed a number that's off by 1, 10, or 100, the toast says so (`OFF BY 10!`). If you got the magnitude right but the sign wrong, it says `WRONG SIGN!`.
- **Adaptive weak-spot weighting** — in versus mode, operations you miss more often are sampled more often. Get them right and the weighting decays. Per-device, no syncing.
- **Lifetime stats** — total problems solved, best streak ever, and matches played persist on the title and game-over screens (localStorage, per browser).
- **Persistent problem-history dedup** — the last 50 problems you've seen are saved per browser. Each new round retries up to 10 times to find a problem not in that list. Yesterday's problems won't immediately repeat today, especially at small early-game ranges where the problem pool is tight (only 81 single-digit-add combinations, etc.).
- **Head-to-head records** — when both players have real names (not the default `PLAYER 1` / `PLAYER 2`), every match's win is recorded under that pair. Title screen shows current record above the names line; game-over screen shows the freshly-updated record. Each name is independent: rename to start a fresh ledger or revert to a previously used name to pick its history back up.

## CPU opponent (1P mode)

The CPU's "thinking time" and accuracy scale with the chosen difficulty:

| Mode | CPU avg think | CPU accuracy |
|---|---|---|
| KIDS | ~4.0s | 78% |
| FAMILY | ~2.6s | 86% |
| GENIUS | ~1.6s | 92% |

When the CPU misses, it's off by 1 or 10 (sometimes negative) — same kinds of mistakes a person makes — instead of giving wildly wrong answers.

## Player names

Press **N** on the title to enter names. Stored in this browser's localStorage, so they stick across sessions on the same device. At another house / browser, names default back to PLAYER 1 / PLAYER 2 until you set them again.

### Named-player phrases (game-over easter egg)

Two named players currently trigger custom one-liners on the game-over screen, each with a **25% probability** so they stay rare and fun (not noise):

- **MIMI loses** → 25% chance of one of her loss reactions (silverware, Tom Brady, etc.). 75% she just sees the normal "X WINS!" view.
- **TREY beats MIMI** → 25% chance of a victory phrase (Brodal Walker Wins!, Broc-Lee Victory, etc.). Only fires when the opponent is Mimi — Trey beating anyone else gets normal cheering.

The two rolls are independent, so a Trey-vs-Mimi match-up can show both lines, just one, or neither.

To edit phrases or change the probability, see `NAME_PHRASES` and `PHRASE_CHANCE` near the top of `index.html`. Names must match in UPPERCASE (the game uppercases names on entry automatically).

## Deploy free on Vercel

Pure static HTML — no build needed.

### Easiest: drag-and-drop

1. Sign up at [vercel.com](https://vercel.com) (free hobby tier).
2. From your dashboard → **Add New → Project**.
3. Click "Deploy" with no template, then drag this whole `math-pong` folder into the upload area (or use the import-folder option).
4. Vercel hands you a URL like `math-pong-xyz.vercel.app`. Done.

### Or: Vercel CLI

```bash
npm i -g vercel
cd math-pong
vercel
```

Accept the defaults. The project is detected as static and goes live in seconds.

### Or: GitHub

Push the folder to a public GitHub repo, then "Import Project" in Vercel — every push to `main` auto-deploys.

## Files

- `index.html` — the entire game (HTML + CSS + JS in one file)
- `README.md` — this file

That's it. No dependencies, no node_modules, nothing else needed.
