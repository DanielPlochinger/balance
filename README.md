# Balance

A personal calorie ledger that runs entirely on your phone. Calories are treated
like money: stay under budget and the surplus is **deposited** in your calorie
bank; go over and you take on **debt** that auto-repays; plan a **cheat day**
and it gets financed — bank first, then small trims off the following days,
never below your floor. Once locked, it's locked: a planned feast, not a broken
streak.

## How it works

- **App**: a single-file PWA (`index.html`), hosted on GitHub Pages. Open it in
  Safari on the iPhone → Share → *Add to Home Screen* → it behaves like a native
  app, offline included.
- **Logging**: dictate or type ("two cappuccinos, one banana, 60 g oats…") —
  the app sends the text to the Claude API, which splits it into items with
  calories and protein. Foods saved in **your library always override Claude's
  estimates**. Unknown foods can be saved to the library in one tap, so the
  tool learns your diet.
- **Data**: synced to the private GitHub repo `balance-data` as `state.json`
  (every change is a commit — full history forever). The phone keeps a local
  copy, so logging works offline and syncs later.
- **Weight**: log it each morning; the trend chart shows raw dots plus a
  smoothed line (exponential moving average), so water-weight noise never
  ruins your day.

## Files

| File | Purpose |
|---|---|
| `index.html` | The entire app — UI, logic, parsing, sync, charts |
| `manifest.webmanifest` | PWA manifest (name, icons, standalone display) |
| `sw.js` | Service worker — offline shell, network-first updates |
| `icon-180.png` / `icon-512.png` | Home-screen icons |

## Local preview

```bash
python3 -m http.server 8899
```

Then open `http://localhost:8899/index.html?demo=1` — demo mode seeds five
weeks of sample data and stubs the Claude call, touching only a separate
localStorage key.

## Keys

The Claude API key and GitHub token are entered in the app's Settings and kept
in `localStorage` on each device only — they are never written into the synced
data or this repo.
