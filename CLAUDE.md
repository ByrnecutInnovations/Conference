# CLAUDE.md

Guidance for Claude (or any developer) working on this repo.

## What this is

Real-time conference Q&A app for Byrnecut. One self-contained `index.html` (HTML + CSS + an ES-module `<script>`) talking directly to **Firebase Realtime Database** (project `conference-qa-2375a`). No build step, no backend, no package.json. Hosted on GitHub Pages from `main`.

Repo: https://github.com/ByrnecutInnovations/Conference

| File | Purpose |
|------|---------|
| `index.html` | The whole app — all three views |
| `README.md` | User/dev docs, live + test URLs, Firebase schema |
| `Moderator-Quick-Guide.html` | Printable one-page guide for moderators (non-technical audience) |
| `CHANGELOG.md` | Record of changes — **update it with every change** |
| `qr-code.png` | QR code to the audience URL |

## Views (selected by URL query string)

| URL | View | Render function |
|-----|------|-----------------|
| `/` | Audience (phones) | `renderAudienceView` → `renderQuestionForm` / `renderClosedView` |
| `?moderator=true` | Moderator dashboard (password) | `renderModeratorLogin` / `renderModeratorView` |
| `?display=true` | Big screen / projector | `renderDisplayView` |

`bootstrap()` at the bottom of the script picks the view.

## Firebase data

```
questions/<id>   {name, question, timestamp, status: pending|approved|answered}
settings/        {sessionOpen, autoApprove, rateLimitSeconds, passwordHash, passwordSalt}
discussion/      {showOnAudience, showOnDisplay, points/<id>/{text, order}}
archive/session_<ts>/ {timestamp, date, questions}
```

- All views subscribe with `onValue(...)` so everything updates live — keep new features live too (no reload required).
- Branch test links (raw.githack.com) and local servers hit the **same production database**. Avoid destructive test actions (e.g. Archive & Clear) while a real session is running.
- Database security rules live in the Firebase Console, not this repo. A new top-level node may need a rules update if writes fail with `PERMISSION_DENIED`.

## Conventions

- **Keep it single-file.** No frameworks, bundlers or new dependencies. Firebase SDK is imported from gstatic at a pinned version (10.7.1).
- UI is built with template strings + `innerHTML`. **Always** pass user/DB text through `escapeHtml()`. IDs embedded in inline `onclick` handlers must pass `isValidQuestionId()` and go through `escapeJsString()`.
- Inline handlers call functions attached to `window.*` (module scope isn't global).
- Moderator actions call `updateLastActivity()` (30-minute inactivity timeout).
- Brand colours: orange `#F5A623`, charcoal `#2D2D2D`. Green `#28a745` = on/approve, red `#dc3545` = danger, grey `#6c757d` = secondary/off.
- On/off toggles are buttons whose text reads `...: ON/OFF` and class flips between `btn-success` / `btn-secondary`.
- Audience view must work well on phones; Display view must be readable from the back of a room.

## Running locally

ES modules don't load from `file://`. Serve the folder:

```
python -m http.server 8080
```

Then open `http://localhost:8080`, `/?moderator=true`, `/?display=true` in separate tabs. VS Code Live Server also works (`.vscode/` is local-only, not committed).

There are no automated tests — verify by hand across all three views.

## Workflow

1. Branch from `main` (`git checkout -b <feature>`), never commit features straight to `main`.
2. Make changes; update `README.md`, `Moderator-Quick-Guide.html` (if moderator-facing) and `CHANGELOG.md` (under *Unreleased*).
3. Update the README "Live Test URLS" to the new branch name, push, and test via
   `https://raw.githack.com/ByrnecutInnovations/Conference/<branch>/index.html`.
4. Open a PR into `main`; merge once tested. On merge, date the *Unreleased* changelog section.
