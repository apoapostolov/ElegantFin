# ElegantFin fork (apoapostolov/ElegantFin)

Personal integration fork of `lscambo13/ElegantFin`. Upstream stays the
reference for new features; this fork is where Apo's own fixes live and where
hand-picked upstream work lands. Never PR `next` upstream as-is; send single
features up from topic branches cut off `main`.

## Current upstream pin (verification stamp, update in place)

- Upstream: `lscambo13/ElegantFin`, remote `upstream`
- Pinned at: `9d43fa9b898c74055237133b7a7b8b8c5543f0ce` (2026-09-09,
  right after the v26.09.05 release)
- `main` mirrors upstream exactly (zero unique commits). `next` is the
  working line, rebased/merged forward as upstream moves.

## Branch map

- `main` — byte-mirror of upstream. Never commit here.
- `next` — integration line: Apo's fixes + accepted experiment lines.
  This is what the live server serves (see Deployment).
- `feat/item-detail-trailer-backdrop`, `feat/item-detail-trailer-random-start`
  — frozen record of the closed upstream PRs #304/#305. Do not rewrite.
- `login-page-v2`, `pr-185` — mirrored upstream experiment branches.

## What `next` carries on top of the pin

- 8 trailer commits (`63c470c`..`0058a07`): item-detail trailer backdrop
  add-on, random start (off by default), three unclip/full-bleed episode-row
  fixes, trailer/mask extension 275→305, branding-hacks refactor, docs.
- `login-page-v2` merged (`1b65fbb`): kept current upstream styling
  everywhere it had evolved (font imports, menu-button rules, login card);
  grafted only the new slideshow pieces (`@keyframes slideshow`, banner
  `::before`, user-card/form-width rules, `banner-light.png`). Repaired a
  broken `https: //` URL typo in the old keyframes during the merge.
- `pr-185` merged as a no-op (already shipped upstream).

## Deployment

BEDROOM Jellyfin (`192.168.1.217:8096`) loads the theme through the
JavaScript Injector plugin entry `ElegantFin-next`, which appends a
stylesheet link to
`https://raw.githubusercontent.com/apoapostolov/ElegantFin/next/Theme/ElegantFin-theme-nightly.css`.
Raw GitHub caches for ~5 minutes, so pushes to `next` land fast (jsdelivr
would pin the branch tip for hours). Revert = disable that injector entry.
Apo's trailer fixes also run as injector scripts
(`ElegantFin-ItemDetailTrailerBackdrop`, `Kefin-SeriesEpisodes-OverflowFix`).

## Comparing with future upstream (procedure, not log)

1. `git fetch upstream` (remote already configured in this clone).
2. `git log --oneline origin/main..upstream/main` — new upstream work.
3. `git diff --stat origin/main...upstream/main` — where it touches.
4. Move `main` first: `git checkout main && git merge --ff-only
   upstream/main && git push origin main` (only if `main` has no unique
   commits; check with `git rev-list --count upstream/main..origin/main`).
5. Cherry-pick single features onto `next` with `git cherry-pick -x
   <sha>`; resolve toward current `next` styling, never resurrect code the
   refactor already moved (cf. the web_routers lesson on hermes-agent).
6. Update the pin stamp above in place and push `next`. The server picks
   it up within minutes; hard-refresh (F5) the web client to see it.
