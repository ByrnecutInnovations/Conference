# Changelog

All notable changes to the Byrnecut Conference Q&A app are recorded here.
Newest first. Dates are YYYY-MM-DD. Format loosely follows [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased] (branch `export-reports`)

### Added
- **Export / Report** view (`?export=true`, moderator password), opened from a new dashboard button. Filter by source (current / archived / both), date range (defaults to the latest day with questions) and status. Group by discussion topic or list in the order asked. Live preview, then download a self-contained branded **HTML report**, **Print / Save as PDF**, **CSV** (UTF-8, topic column, formula-safe) or **JSON**.
- Questions now record the discussion topics that were ON (and shown on Audience or Display) at submit time, in `questions/<id>/topics`. Topic text is stored, so later edits/deletes of topics don't affect old questions. Shown as a "Topic:" tag on moderator cards.

- **Display title** setting on the Moderator dashboard (`settings/displayTitle`): changes the big-screen heading live; blank falls back to "Q&A". Export reports default to it as their title.

### Changed
- Display empty state now reads "No submissions." / "Waiting on responses" (from a direct edit on GitHub `main`, merged in; typo fixed). The hard-coded event title from that edit is replaced by the Display title setting.

### Notes
- **Firebase rules:** add `settings/displayTitle` (`.write: true`, string ≤ 100 chars) and publish in the Console **before merging**, or saving the title fails with `PERMISSION_DENIED`. Topics on questions and the Export view need no rules change (`questions/` has no field validation; `archive/` is readable).
- Questions submitted before this change have no topic history and report as "No discussion topic recorded".
- `Export_data/` (raw Firebase exports with attendee data) added to `.gitignore`.

## 2026-11-16 — Per-topic discussion toggles (branch `discussion-topic-toggles`)

### Added
- Per-topic **ON/OFF** button for each discussion point, so the full day's agenda can be loaded up front and topics revealed as the conference progresses. Audience and Display update live.
- **Show all** / **Hide all** buttons and an "X of Y topics showing" count.
- Add several topics at once: the add box is now multi-line (one topic per line; pasted bullets/numbering are stripped). Enter adds, Shift+Enter starts a new line.

### Changed
- New topics start **OFF**. Existing points with no `visible` field are treated as ON, so nothing already on screen disappears.
- Firebase: new optional `discussion/points/<id>/visible` boolean.
- Firebase rules: `visible` added to the points validation (rules reject unknown fields). **Must be published in the Firebase Console before testing or merging.**
- README and Moderator Quick Guide updated.

## 2026-11-14 — Discussion points (branch `discussion-points`)

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
- README: documented discussion points and the new database node; test links section is now a generic `<branch>` template.
- Moderator Quick Guide: added discussion point steps to before / during / after the session; date updated to November 2026.

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
