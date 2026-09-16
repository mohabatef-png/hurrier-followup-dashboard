# Hurrier Dashboard — Automated Data Setup

This replaces the bookmarklet with a **GitHub Action** that fetches live data
from the Talabat API every ~5 minutes and pushes it to your repo automatically.
Your dashboard HTML (`index.html`) already reads those JSON files — no changes
needed there.

---

## How it works

```
GitHub Actions (every 5 min)
  └─▶ fetch-hurrier.js
        ├─ calls Talabat API  →  /api/dispatcher-dashboard/deliveries/active
        ├─ calls Talabat API  →  /api/dispatcher-dashboard/courier/detail
        ├─ calls Talabat API  →  /api/cash-collection/v1/couriers/{id}/balance
        └─ writes data-<hub-slug>.json  →  git push

Dashboard (in browser)
  └─▶ fetches raw.githubusercontent.com/…/data-<hub-slug>.json
        every 60 seconds (built-in countdown timer in the HTML)
```

---

## Setup steps

### 1 — Copy files into your repo

Copy these two files into your repo (`mohabatef-png/hurrier-followup-dashboard`):

```
.github/workflows/fetch-data.yml   ← the Action
scripts/fetch-hurrier.js           ← the data fetcher
```

### 2 — Add your API token as a GitHub Secret

1. Go to your repo on GitHub
2. **Settings → Secrets and variables → Actions → New repository secret**
3. Name:  `TALABAT_API_TOKEN`
4. Value: your Bearer token (without the word "Bearer")
5. Click **Add secret**

> ⚠️  Never put the token directly in any file — the secret keeps it safe.

### 3 — Set your API base URL (if needed)

If your Talabat API is at a different host than `https://dispatcher.talabat.com`,
add another secret (or a repo variable) called `TALABAT_API_BASE` with the
correct base URL, then add it to the workflow's `env:` block:

```yaml
env:
  TALABAT_TOKEN:    ${{ secrets.TALABAT_API_TOKEN }}
  TALABAT_API_BASE: ${{ secrets.TALABAT_API_BASE }}
```

### 4 — Configure your hubs

Edit `scripts/fetch-hurrier.js` → the `HUBS` array near the top.
Each hub needs:
- `slug`  — must match the slugs in your dashboard's `HUBS` array in `index.html`
- `spIds` — the starting_point_ids for that hub

The file already has `maadi-mokattam-helwan` configured with the same IDs the
bookmarklet was using. Uncomment and fill in the others as needed.

### 5 — Enable Actions & test

1. Push the files to your `main` branch
2. Go to **Actions** tab in GitHub
3. Click **"Fetch Hurrier Data"** → **"Run workflow"** to trigger it manually first
4. Check the logs — you should see orders fetched and files committed
5. After that it runs automatically every 5 minutes

---

## Refresh frequency

| Layer | Frequency | Why |
|---|---|---|
| GitHub Action | Every 5 min | GitHub's minimum for scheduled Actions |
| Dashboard HTML | Every 60 sec (3-min countdown / manual) | Reads the latest JSON from GitHub raw |

So data is at most ~5 minutes old. The dashboard refreshes itself from the
file every minute, so users always see the freshest available snapshot.

If you need fresher data (e.g. every 1 min), you can run the Action on a
separate VM/cron job and call it via `workflow_dispatch` API — ask and I'll
set that up too.

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| Action fails with 401 | Token expired or wrong — update the `TALABAT_API_TOKEN` secret |
| No data files created | Check the Action logs for the actual API error message |
| Dashboard shows "Could not load" | Check that the data-*.json files exist in the `main` branch |
| Data is stale | Check Actions tab — is the schedule running? GitHub sometimes delays cron by 15–30 min |

---

## Security improvements vs the old bookmarklet

| Old (bookmarklet) | New (GitHub Action) |
|---|---|
| Token hardcoded in plain text | Token stored in GitHub Secrets (encrypted) |
| Intercepts your browser session | Uses a proper API token — no interception |
| Manual — someone must run it | Fully automatic |
| Only works on one person's browser | Runs in the cloud, always on |
