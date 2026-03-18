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
- Submissions are rate-limited to one question per minute per browser
- The page automatically re-enables the form when the cooldown expires
- Questions go into Firebase with status `pending`

### Moderator view (`?moderator=true`)
- Password-protected dashboard (session expires after 30 minutes of inactivity)
- Approve, reject, or mark questions as answered
- Filter questions by status: All / Pending / Approved
- Open or close the session (prevents new audience submissions when closed)
- Archive and clear all questions at end of session
- Download questions as a CSV file
- Change the moderator password (generates a new hash/salt to paste into Firebase Console)

### Display view (`?display=true`)
- Shows all approved questions in real time — intended for a projector or second screen
- Each question has a **Mark Answered** button so the presenter can dismiss questions directly from the display without switching to the moderator dashboard

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
  sessionOpen   — boolean (true = accepting questions)
  passwordHash  — SHA-256 hash of moderator password
  passwordSalt  — random hex salt for the hash

archive/
  session_<timestamp>/
    timestamp
    date
    questions/  — snapshot of questions at time of archive
```
