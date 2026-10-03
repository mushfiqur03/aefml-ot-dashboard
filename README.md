# AEFML — Live OT Dashboard (GitHub Pages)

Live overtime dashboard for Akij Essentials Ltd (AEFML), hosted on **GitHub Pages**,
with data pulled live from Google Sheets via a Google Apps Script JSONP API.

---

## Architecture

```
Google Sheet (ArlOpexDB source tabs)
        │
        ▼
Google Apps Script  (Code.gs)  ── doGet(?data=1&callback=) ──►  JSONP
        │
        ▼
GitHub Pages  (index.html)  ── reads JSONP ──►  Live dashboard
```

- `index.html` — the dashboard (static). Works on GitHub Pages **and** inside Apps Script.
- `Code.gs.txt` — the Apps Script backend (paste into Apps Script as `Code.gs`).
- `.nojekyll` — tells GitHub Pages to skip Jekyll processing.

---

## Setup (one time, ~10 min)

### 1. Deploy the Apps Script backend
1. Go to <https://script.google.com> → your OT dashboard project (or new project).
2. Paste `Code.gs.txt` → file named **`Code.gs`**.
3. Paste the dashboard HTML → file named **`Index.html`**
   (same content as `index.html`).
4. **Deploy → New deployment → Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Copy the **Web app URL** — it looks like:
   `https://script.google.com/macros/s/AKfy.../exec`

### 2. Point the dashboard at the backend
1. Open `index.html` and find:
   ```js
   var API_URL = "";   // <-- paste /exec URL here
   ```
2. Paste your `/exec` URL inside the quotes.
3. (Optional) If you set `DASH_KEY` in `Code.gs`, put the same value in `API_KEY`.

### 3. Publish on GitHub Pages
```powershell
cd "$env:USERPROFILE\Desktop\OT-Dashboard-GitHub"
git init
git add .
git commit -m "AEFML OT dashboard"
gh repo create aefml-ot-dashboard --public --source . --push
gh api -X POST repos/:owner/aefml-ot-dashboard/pages -f "source[branch]=main" -f "source[path]=/"
```
Your dashboard will be live at:
`https://<your-username>.github.io/aefml-ot-dashboard/`

---

## ⚠️ Security notice

This dashboard shows **internal Akij Resource data** (section-wise OT hours and
BDT amounts). A **public** GitHub Pages site can be viewed by anyone with the URL.

Options:
- **Recommended:** set `DASH_KEY` in `Code.gs` and `API_KEY` in `index.html`
  (deters casual access; note the key is visible in page source).
- Use a **private** repo + GitHub Pro for private Pages.
- Or keep using the Apps Script web app only (access controlled by Google login).

---

## Monthly maintenance

**Nothing to change every month** — `AUTO_DETECT` finds the latest 2 months
automatically from tab names (`SummaryAEFML(HR)-MMM`).

Only edit when:
- Tab naming pattern changes → set `AUTO_DETECT: false` in `Code.gs` `CONFIG`.
- Production tab name changes → update the name in `readProduction()`.

## Auto-refresh
The dashboard refreshes itself every **5 minutes**; use the 🔄 button for an instant refresh.
