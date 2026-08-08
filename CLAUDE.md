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

- v2.1 look (research-driven: Liquid Glass + M3 Expressive + ADA winners):
  pure white ground, tonal `#F5F5F3` cards (no borders/shadows), accent-washed
  hero panel with a giant SF Rounded number + capsule meters (no ring),
  capsule buttons, Liquid-Glass floating dock with accent-filled active pill,
  springy motion (`--spring` overshoot bezier). **Light theme only** — dark
  mode was deliberately removed at Daniel's request. Per-view accents:
  Today teal `#089171`, Trends indigo `#4F5BE8`, Foods coral `#E14B2E`,
  Settings gold `#B57E0A`, via `body[data-view]`; `--tint`/`--wash` washes
  via color-mix.
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
