# Balance

A personal calorie-ledger PWA. Single file, no frameworks, no build step —
`index.html` is the whole app. Hosted on GitHub Pages; add it to an iPhone
home screen and it runs standalone — no computer involved.

- **Data**: a single `state.json` in a private repo is the only truth, read
  and written through the GitHub Contents API. Each device keeps a copy for
  display; offline the app reads but cannot log.
- **Food logging**: dictated or typed text, parsed by the Claude API directly
  from the browser. Personal food library overrides the model's estimates.
- **Keys**: Claude API key and GitHub token are entered in the app's Settings
  and stored only in that device's localStorage — never in this repo.

Local preview: `python3 -m http.server 8899`, then open
`http://localhost:8899/index.html?demo=1` (demo mode: seeded data, stubbed
parser, separate storage).
