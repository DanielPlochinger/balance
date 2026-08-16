# Balance

A personal calorie-ledger PWA. Single file, no frameworks, no build step —
`index.html` is the whole app. Hosted on GitHub Pages; add it to an iPhone
home screen and it runs standalone, offline included.

- **Data**: synced to a private repo via the GitHub Contents API; the app is
  local-first (each device holds a full copy).
- **Food logging**: dictated or typed text, parsed by the Claude API directly
  from the browser. Personal food library overrides the model's estimates.
- **Keys**: Claude API key and GitHub token are entered in the app's Settings
  and stored only in that device's localStorage — never in this repo.

Local preview: `python3 -m http.server 8899`, then open
`http://localhost:8899/index.html?demo=1` (demo mode: seeded data, stubbed
parser, separate storage).
