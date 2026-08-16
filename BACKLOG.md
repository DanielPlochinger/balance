# Backlog (not version notes — just so nothing is forgotten)

- Someday: derive maintenance from intake vs weight trend (parked)

## Architecture maturity — sound for a single-user early version; revisit only if the tool grows

- Server-side proxy for the Claude key (today: browser-direct calls, key per device)
- In-app "update available" prompt (today: cache-bump + double-relaunch ritual)
- Real backend/database (today: GitHub repo as single-truth store — principled at this scale)
