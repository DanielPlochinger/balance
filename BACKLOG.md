# Backlog (not version notes — just so nothing is forgotten)

- Settings › Data: show the latest backup (date · time) after creating one (Daniel, Aug 16)
- Today: consider equal-sized "Log with Claude" / "Manual" buttons; consider switching their order (Daniel, Aug 16)
- Sheets: floating window instead of edge-attached — minimal side margins, all four corners rounded, ALL behavior unchanged (flexible height, footers, scroll-edge, keyboard slide-up works identically — verified reasoning, no caveats). Decide by eye in demo during that version's audit. (Daniel + Claude aligned, Aug 16)
- Foods restructure (Daniel, Aug 16 — direction agreed, details for the version discussion):
  - Tap a food → always the same amount pop-up, unit-aware (items / g / ml); type 1, 5, 300, whatever → adds to today. One behavior, no per-basis special cases; the pop-up doubles as accidental-tap protection (plus the undo toast).
  - Editing separates from tapping: Claude recommends swipe-left on row → Edit / Delete (long-press as simpler fallback).
  - Stars unchanged (favorites → Today quick-chips); starred-first then A–Z sorting unchanged; the › arrows are removed (tap no longer navigates).
- Someday: derive maintenance from intake vs weight trend (parked)

## Architecture maturity — sound for a single-user early version; revisit only if the tool grows

- Server-side proxy for the Claude key (today: browser-direct calls, key per device)
- In-app "update available" prompt (today: cache-bump + double-relaunch ritual)
- Real backend/database (today: GitHub repo as single-truth store — principled at this scale)
