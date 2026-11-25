# CLAUDE.md — Music Box

A generative music toy: one full-screen canvas showing a cutaway "music factory" of 8 rooms, each playing one part of a tune made with the Web Audio API. Linked from the Music Box tab on krippy.ai.

There's no spec file yet. Read `index.html` around the code you're changing before you change it. If an issue changes how the toy behaves, describe the new behaviour in the PR.

## Hosting

- **Repo:** https://github.com/kripsterama/musicbox (branch `main`)
- **Hosting:** GitHub Pages with a custom domain via `CNAME` → `musicbox.krippy.ai`
- **`main` is live.** Every push to `main` goes straight to the public site.

## Ground rules

- **Never push to `main`.** Always work on a branch and open a pull request. Anil reviews and merges.
- Branch name: `claude/<linear-issue-id>-<short-slug>` (e.g. `claude/KRI-12-volume-slider`).
- One issue = one PR. If the work is bigger than one reviewable PR, stop and say so on the issue, with a proposed split, instead of building it.
- If the issue is unclear or a decision is needed (sound, look, copy), ask on the issue and stop. Don't guess.

## The codebase

- **No build step, no dependencies.** The whole app is `index.html` (~550 lines): markup, CSS and JS together. Keep it one file; don't add a bundler, framework or npm.
- The JS is written in a **compact style**: short names (`AC`, `osc`, `vca`, `rr`, `P`), long lines, little whitespace. Match it in the code you touch. Don't reformat, rename or "clean up" code the issue doesn't ask about; a reformat makes the diff unreviewable.
- External calls, both of which must fail gracefully:
  - Google Fonts (Bungee)
  - CoinGecko price API (BTC, ETH, SOL, HYPE). Prices nudge the music (`mkt`), e.g. BTC's 24h change shifts the tempo in `applyTempo()`. The toy must still work if this fetch fails or is blocked.
- Don't add other external services, trackers or analytics.

## How it works (map)

| Area | Where |
|---|---|
| Palette tokens | `:root` near the top: `--wood`, `--brass`, `--cream`, `--ink`, `--panel`. Dark by design |
| Layout | `layout()` / `fit()`: 8 rooms in a 2×4 grid (portrait) or 4×2 (landscape); order in `ORDER` |
| Tune | `makeTune(seed, num)`: seeded with `mulberry()`, so a given seed always plays the same tune |
| Audio | `initAudio()`, `sched()`, `playStep()`; helpers `osc`, `vca`, `nz`, `filt`. Audio starts only after a user tap (`unlock()`), as browsers require |
| Rooms | `drawClock`, `drawLead`, `drawArp`, `drawPad`, `drawKick`, `drawBass`, `drawDrum`, `drawBell` |
| Render loop | `frame()` |
| Mouse hunt + streak | A hidden mouse in one room per day; the daily streak is stored in `localStorage` under `tw-streak` |
| UI | `#mute` sound button (top right), `#start` sign (centre), toasts via `toast()` |

## Conventions

- `localStorage` access stays wrapped in try/catch.
- Keep `prefers-reduced-motion` handling (`reduce`) intact.
- Keep accessibility: the mute button's `aria-label` and title update with its state, and it keeps its focus outline.
- Don't start audio without a user gesture.
- Keep it smooth on phones: no per-frame allocations in hot paths you add, and respect the existing DPR handling.
- Changing `makeTune` or the seeding changes every tune people know. Only do that if the issue says so.

## Checking your work

There are no automated tests. Before opening a PR:
1. Serve the folder (`python3 -m http.server 8000`) and load it in a headless browser if one is available. Check the console for errors (a blocked CoinGecko or Google Fonts request is expected in a sandbox, so note it and move on).
2. Check both portrait (e.g. 390×844) and landscape (e.g. 1280×800): all 8 rooms draw and the start sign shows.
3. Tap to start and confirm there are no errors once audio starts. You can't hear it, so say so in the PR.
4. Say in the PR exactly what you checked and what you couldn't (sound, real fonts, live prices).

## PR description

- Link the Linear issue.
- What changed and why, in a few lines.
- How you tested it, and what Anil should check by ear and by eye.
