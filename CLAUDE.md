# Claude Code — Balance (calorie ledger PWA)

## What this is

Daniel's personal calorie/weight tracker. Core metaphor: **calories are money**.
v2.4 mechanics (full spec: `V2.4_PLAN.md` §0 — read it before touching the math):
daily settlement vs the **normal budget**; under-budget surplus becomes a
**deposit** (capped 300/day, expires after 10 days, FIFO); overage drains
deposits oldest-first, remainder becomes **debt** with an automatic repayment
schedule (60%/400/900/500 formula — trims shrink following days' targets);
misses re-plan, overpayment shortens; the streak breaks only on a miss or an
unlogged day (debt alone never breaks it — redemption design). No cheat-day
feature (removed in v2.4). Per-day settings **snapshots** (`b/c/g/m`) make
closed days immutable; weekly closes are physiological (vs maintenance,
7,700 kcal/kg). No protein anywhere (removed in v2.4). Everything except day
records + snapshots is derived — never persist ledger/plan state.

## Architecture (do not re-litigate)

- **No Mac dependency at runtime.** Daniel's Mac is a laptop and often asleep;
  the app must work standalone on the iPhone. Never propose a Mac-hosted server.
- Single-file PWA: `index.html` holds all UI + logic. No frameworks, no build
  step, no npm. Keep it that way.
- Hosting: GitHub Pages from the public repo `DanielPlochinger/balance`
  (code only — never put data or keys there).
- Data (v2.5, **remote truth** — spec `V2.5_PLAN.md`): `state.json` in the
  **private** repo `DanielPlochinger/balance-data` is the ONLY truth. Local
  storage is a display cache, never merged back. All mutations funnel through
  `write(mutator)`: fetch fresh → mutate → PUT with sha; conflict → refetch +
  reapply (≤3). In-flight writes serialize. Offline = read-only (no queues).
  There is NO merge engine, no tombstones, no wipe-signals — do not
  reintroduce local-first machinery; that bug class was removed deliberately
  after repeated data-loss incidents. Backups = dated snapshots under
  `backups/` in the same repo; restore is a normal write.
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
- No browser-native `alert`/`confirm`/`prompt` — the bottom-sheet (`sheet()`)
  is the only modal.
- Weight trend: raw dots + EMA (α=0.3) line.
- Charts: numbers appear only for truths (entered values, goals); trend lines
  are purely visual — direction and rate, never labeled. No axis-bound labels.
- Weekly close: net kcal vs snapshotted maintenance ≈ kg at 7,700 kcal/kg (v2.4).
- Demo mode: `?demo=1` — separate localStorage key, seeded data, stubbed LLM.
  Keep it working; it's how the app is tested without keys.

## Version plan docs (the process — Daniel, locked 2026-08-16)

Every version — features and bugfix patches alike — gets a **plan doc**:
`V<version>_PLAN.md`, kept in this folder forever as the inventory of what
that version changed. **Never invent a version number or create a plan doc
without asking Daniel first.**

Lifecycle (all stages happen IN CONVERSATION; the doc only ever contains
outcomes):
1. **Blank start.** The doc begins empty — never pre-populate outlines,
   candidate lists, or backlog items. Backlog lives in `BACKLOG.md`, never in
   version notes.
2. **Discuss.** Bugs/changes are raised and honed in chat. Proposals and
   alternatives stay in chat; only what Daniel approves enters the doc, marked
   LOCKED with date.
3. **Audit.** Before execution: check every item against the existing code for
   structural breakage and for reuse of existing objects/patterns; report
   findings in chat; fold Daniel's rulings into the doc.
4. **Execute once**, on Daniel's explicit go, from the doc alone.
5. **Ship record.** Append a short shipped note (version, date, tag) when done.

Writing standard: for an implementer with no chat context — the doc alone must
suffice to build faithfully. Bugs as symptom → cause → fix; changes as
imperative bullets; a one-line *intent* note wherever intent constrains an
ambiguous implementation choice. Distilled decisions only — never
conversational back-and-forth, attribution, or unapproved proposals.
Reference examples: `V2.4_PLAN.md`, `V2.4.3_PLAN.md`.

## Data deletion policy (Daniel, 2026-08-15)

When Daniel asks to delete data, he means **unrecoverable**: edit the single
truth (`state.json`) AND rewrite `balance-data` git history (orphan commit,
force push), including any `backups/` snapshots that contain the deleted
data. Never retain old states "just in case" and never pitch git history as
a recovery feature. Under remote truth (v2.5) device caches self-heal on
their next fetch — no wipe signals needed.

## Deploying

Commit in this folder, push to `main` of `DanielPlochinger/balance`;
GitHub Pages serves it. Bump the `CACHE` version string in `sw.js` whenever
shipping user-visible changes, or phones will serve the stale shell.

## Testing

`.claude/launch.json` (in the invoice-app working folder) has a `balance`
config: `python3 -m http.server 8899` serving this folder. Open
`/index.html?demo=1`, mobile viewport 375×812. Light theme only.
