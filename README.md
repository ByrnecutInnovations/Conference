# Byrnecut Conference Q&A App

A real-time Q&A submission and moderation system for Byrnecut conference sessions. Audience members submit questions via a web form; a moderator reviews and approves them; approved questions appear live on a display screen (e.g. projector).

Built as a single `index.html` file backed by Firebase Realtime Database — no backend server required.

---

## Live URLs (GitHub Pages)

| View | URL |
|------|-----|
| Audience | https://byrnecutinnovations.github.io/Conference/ |
| Moderator | https://byrnecutinnovations.github.io/Conference/?moderator=true |
| Display | https://byrnecutinnovations.github.io/Conference/?display=true |

---

## Live Test URLS
Currently under test: branch `discussion-topic-toggles` (per-topic ON/OFF for discussion points).

| View | URL |
|------|-----|
| Audience | https://raw.githack.com/ByrnecutInnovations/Conference/discussion-topic-toggles/index.html |
| Moderator | https://raw.githack.com/ByrnecutInnovations/Conference/discussion-topic-toggles/index.html?moderator=true |
| Display | https://raw.githack.com/ByrnecutInnovations/Conference/discussion-topic-toggles/index.html?display=true |

Branch test links use the **same live Firebase database** as production. If a branch adds a new database node, the Firebase rules must be updated before it will save (see `CLAUDE.md`).



## Testing Locally

The app cannot be opened via `file://` (Firebase SDK uses ES modules which browsers block on the file protocol). Use any simple local web server instead — the app talks directly to the live Firebase database regardless of where it is hosted, so all real-time features work exactly as in production.

### Option 1 — Python (recommended, no install needed)

```bash
cd /path/to/BAPL-Conference
python3 -m http.server 8080
```

Then open `http://localhost:8080` in your browser.

### Option 2 — VS Code Live Server

1. Install the **Live Server** extension in VS Code
2. Right-click `index.html` in the Explorer panel
3. Select **Open with Live Server**

The browser opens automatically and reloads on every file save — useful during development.

### Option 3 — Node

```bash
npx serve /path/to/BAPL-Conference
```

### Local test URLs

| View | URL |
|------|-----|
| Audience | http://localhost:8080 |
| Moderator | http://localhost:8080/?moderator=true |
| Display | http://localhost:8080/?display=true |

Open all three in separate browser tabs to test the full flow end-to-end.

---

## How It Works

### Audience view (default)
- Attendees enter an optional name and their question (max 500 characters)
- Submissions are rate-limited per browser (default 30 seconds; the moderator can change it live from the dashboard)
- During the cooldown attendees can keep typing their next question; Submit re-enables automatically when it expires
- The page switches between open/closed live when the moderator opens or closes the session — no reload or QR re-scan needed
- Questions go into Firebase with status `pending`

### Moderator view (`?moderator=true`)
- Password-protected dashboard (session expires after 30 minutes of inactivity)
- Approve, reject, or mark questions as answered
- Filter questions by status: All / Pending / Approved
- Open or close the session (prevents new audience submissions when closed)
- **Auto-approve** on/off: when on, new questions go straight to the display (turning it on also approves anything pending) — useful when the moderator is also the presenter
- **Seconds between questions**: set the audience cooldown live (0 = no limit)
- **Discussion Points**: load the whole day's topics up front (paste a list, one per line — leading bullets/numbers are stripped), reorder (↑/↓) or delete them, and switch **individual topics ON/OFF** as the day progresses (plus **Show all** / **Hide all**). New topics start OFF. Choose where the switched-on topics appear with two independent toggles — **Show on Audience** (phones) and **Show on Display** (big screen). Either, both or neither can be on. Points are not removed by Archive & Clear.
- Archive and clear all questions at end of session
- Download questions as a CSV file
- Change the moderator password (generates a new hash/salt to paste into Firebase Console)

### Display view (`?display=true`)
- Shows all approved questions in real time — intended for a projector or second screen
- Each question has a round **✓** (mark answered) button so the presenter can dismiss questions directly from the display without switching to the moderator dashboard
- When **Show on Display** is on, discussion points appear in a panel beside the questions (stacked above them on narrower screens)

### Audience view — discussion points
- When **Show on Audience** is on, discussion points appear as a collapsible list above the question form (and on the "Submission Closed" screen)

---

## Moderator Password

The default password is `123` — **change this before use in production**.

1. Log in to the moderator dashboard
2. Click **Change Password**
3. Verify the current password, then enter the new one
4. Copy the generated **Hash** and **Salt** values
5. In [Firebase Console](https://console.firebase.google.com/) → Realtime Database, navigate to `settings/` and update `passwordHash` and `passwordSalt`

---

## Firebase Database Structure

```
questions/
  <push-id>/
    name        — submitter name (or "Anonymous")
    question    — question text
    timestamp   — Unix ms
    status      — "pending" | "approved" | "answered"

settings/
  sessionOpen       — boolean (true = accepting questions)
  autoApprove       — boolean (true = new questions skip moderation)
  rateLimitSeconds  — integer 0–600 (audience cooldown; default 30 if unset)
  passwordHash  — SHA-256 hash of moderator password
  passwordSalt  — random hex salt for the hash

discussion/
  showOnAudience  — boolean (show points on audience phones)
  showOnDisplay   — boolean (show points on the big screen)
  points/
    <push-id>/
      text   — bullet point text (max 200 chars)
      order  — integer sort position (1 = top)
      visible — boolean (false = hidden from Audience/Display; missing = shown, for older points)

archive/
  session_<timestamp>/
    timestamp
    date
    questions/  — snapshot of questions at time of archive
```
