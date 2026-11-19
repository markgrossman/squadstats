# Squad Stats

Static Fortnite stats page. A GitHub Action fetches stats from fortnite-api.com and commits `data/stats.json` and `data/history.json`; `index.html` just reads those files.

## Setup
1. Push this repo to GitHub.
2. Settings → Secrets and variables → Actions → add `FORTNITE_API_KEY` (the key from dash.fortnite-api.com, used as-is).
3. Settings → Pages → deploy from the `main` branch, root folder.
4. Actions tab → "Update stats" → Run workflow to populate data immediately.
5. Edit `players.json` to add friends (`accountType`: `epic`, `psn` or `xbl`). Each player must have career stats set to public in Fortnite's privacy settings.

## Each new season
Edit `season.json` with the new `start` and `end` dates (`YYYY-MM-DD`). The stats API doesn't report them, and the countdown and the squad-goal pace markers depend on them. Adjust `goals.json` targets at the same time.

## Keeping it fresh
The workflow has a `*/10` cron, but GitHub throttles scheduled workflows on busy runners, so in practice they can land hours apart. For reliable updates, have an external scheduler (cron-job.org, a Cloudflare Worker cron, etc.) trigger the workflow every 10–15 minutes:

```
POST https://api.github.com/repos/markgrossman/squadstats/actions/workflows/update-stats.yml/dispatches
Authorization: Bearer <fine-grained token with Actions: read & write on this repo>
Accept: application/vnd.github+json
Body: {"ref":"main"}
```

`stats.json` carries two timestamps: `updatedAt` (numbers last changed) and `checkedAt` (last successful check, refreshed at most every 30 minutes to avoid commit spam). The page shows both.

## Local test
```
FORTNITE_API_KEY=your-key node scripts/fetch-stats.mjs
python3 -m http.server
```
