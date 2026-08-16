# Backlog (not version notes — just so nothing is forgotten)

- Settings › Data: show the latest backup (date · time) after creating one (Daniel, Aug 16)
- Today: consider equal-sized "Log with Claude" / "Manual" buttons; consider switching their order (Daniel, Aug 16)
- Sheets: consider floating window instead of edge-attached — minimal left/right margins, all behavior unchanged incl. flexible height (Daniel, Aug 16)
- Foods restructure consideration (Daniel, Aug 16): tap a food = add to today's meals; editing becomes a separate function — how? Open questions: role of stars (today: favorites → quick-chips; list sorts starred-first then A–Z), whether the › edit-arrows survive the restructure
- Someday: derive maintenance from intake vs weight trend (parked)

## Architecture maturity — sound for a single-user early version; revisit only if the tool grows

- Server-side proxy for the Claude key (today: browser-direct calls, key per device)
- In-app "update available" prompt (today: cache-bump + double-relaunch ritual)
- Real backend/database (today: GitHub repo as single-truth store — principled at this scale)
