# Changelog

All notable changes to the Byrnecut Conference Q&A app are recorded here.
Newest first. Dates are YYYY-MM-DD. Format loosely follows [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased] — branch `discussion-points`

### Added
- **Discussion Points** panel on the Moderator dashboard: add bullet points, reorder them (↑/↓), delete them (with confirmation).
- Two independent toggles, **Show on Audience** and **Show on Display**, so points can be shown on phones, the big screen, both or neither.
- Audience view shows points as a collapsible list above the question form and on the "Submission Closed" screen.
- Display view shows points in a side panel next to the questions (stacked above them below 1000px wide).
- New Firebase node `discussion/` (`showOnAudience`, `showOnDisplay`, `points/<id>/{text, order}`).
- `CLAUDE.md` (project notes for AI-assisted editing) and this `CHANGELOG.md`.
- `.gitignore` — keeps the local Firebase rules copy (`firebase-rules.json`) and `.vscode/` out of the repo.
- Firebase rules: new `discussion` block (booleans for the toggles; points need `text` 1–200 chars and numeric `order`). Must be published manually in the Firebase Console.

### Changed
- README: documented discussion points, new database node, and test links now point at the `discussion-points` branch.
- Moderator Quick Guide: added discussion point steps to before / during / after the session.

### Notes
- Discussion points are **not** cleared by *Archive & Clear All* — they persist until deleted.
- Max 200 characters per point.

## 2026-10-16

### Added
- Moderator Quick Guide (`Moderator-Quick-Guide.html`).
- Moderator settings: live **Seconds between questions** cooldown (0–600, default 30) and **Auto-approve** toggle (turning it on approves anything pending).
- Round ✓ "mark answered" button on each Display card.
- Test links for branch builds in README (via raw.githack.com).

### Changed
- Audience view keeps typed text during the cooldown and across session open/close; switches between open/closed live without reload.
- Audience rate limit is now 30 seconds by default.

## 2026-01 to 2026-03

- Initial single-file app (`index.html`) on Firebase Realtime Database with Audience, Moderator and Display views.
- Salted SHA-256 moderator password, 30-minute moderator inactivity timeout, archive & clear, CSV download.
- QR code for the audience link (`qr-code.png`).
