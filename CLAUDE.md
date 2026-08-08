# Claude Code — Balance (calorie ledger PWA)

## What this is

Daniel's personal calorie/weight tracker. Core metaphor: **calories are money**.
Daily budget, a calorie **bank** (capped deposits from under-budget days, full
debt from over-budget days), and **cheat days** that are financed (bank first,
then trims spread over following days, never below the floor) and **locked**
once confirmed.

## Architecture (do not re-litigate)

- **No Mac dependency at runtime.** Daniel's Mac is a laptop and often asleep;
  the app must work standalone on the iPhone. Never propose a Mac-hosted server.
- Single-file PWA: `index.html` holds all UI + logic. No frameworks, no build
  step, no npm. Keep it that way.
- Hosting: GitHub Pages from the public repo `DanielPlochinger/balance`
  (code only — never put data or keys there).
- Data: `state.json` in the **private** repo `DanielPlochinger/balance-data`,
  written via the GitHub Contents API. Merge strategy: per-day / per-food
  `u` (updatedAt) timestamps, last-write-wins, with a content guard: a day
  holding entries/weight/cheat always beats an empty record regardless of
  timestamps (an auto-created empty "today" once clobbered real entries —
  don't remove this guard). Auto-created day records get `u:0`; only real
  mutations bump `u`. Sha-based optimistic concurrency, one pull-merge-retry.
- LLM parsing: browser-direct call to the Claude API
  (`anthropic-dangerous-direct-browser-access` header), structured outputs
  (`output_config.format` json_schema). Library matches are recomputed
  client-side from the library — the LLM's numbers are only a fallback.
- Secrets (`balance:secrets` in localStorage): Claude API key, GitHub
  fine-grained PAT, repo, model. Never synced, never committed.

## Conventions

- v2.2 look (reference-researched: Things, Flighty, Gentler Streak — Daniel
  rejected warm/"Claude" palettes; cool neutrals only): cool grey ground
  `#F3F5F7`, white structured cards (hairline + soft shadow, 18px radius),
  compact ring + big numeral side-by-side hero (density + drama), capsule
  buttons/chips/dock, dock active = tinted pill. **SF Rounded is reserved for
  numerals** (`.num`/`.heronum2`/`.kpi .v`); titles are heavy system sans.
  **Light theme only** (dark mode removed at Daniel's request). Per-view
  accents (vivid, cool-leaning): Today green `#0FA958`, Trends blue `#2E7CF6`,
  Foods orange `#F26B1D`, Settings steel `#5B6472`; amber `#C77E06` is a
  semantic color for bank/streak only. Never reintroduce warm grey grounds
  or muted gold accents.
- Type: `ui-rounded` (SF Rounded on iOS) for display numbers and titles,
  system sans for UI, tabular-nums throughout.
  No webfonts — zero external requests except GitHub + Anthropic APIs.
- v1.0 look (serif numerals, edge-to-edge tab bar, ledger neutrals) is
  frozen at git tag `v1.0` / GitHub release. Revert:
  `git checkout v1.0 -- . && git commit && git push` (bump sw.js CACHE after).
- Light and dark themes via CSS custom properties; both must stay legible.
- No browser-native `alert`/`confirm`/`prompt` — the bottom-sheet (`sheet()`)
  is the only modal.
- Weight trend: raw dots + EMA (α=0.3) line; the line is "the truth".
- Weekly close: net kcal vs budget ≈ kg at 7700 kcal/kg; deficit shown as −.
- Demo mode: `?demo=1` — separate localStorage key, seeded data, stubbed LLM.
  Keep it working; it's how the app is tested without keys.

## Deploying

Commit in this folder, push to `main` of `DanielPlochinger/balance`;
GitHub Pages serves it. Bump the `CACHE` version string in `sw.js` whenever
shipping user-visible changes, or phones will serve the stale shell.

## Testing

`.claude/launch.json` (in the invoice-app working folder) has a `balance`
config: `python3 -m http.server 8899` serving this folder. Open
`/index.html?demo=1`, mobile viewport 375×812, check light + dark.
