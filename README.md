# Clinical Trials Lead Generation Dashboard

A self-updating dashboard that pulls active clinical trial data from ClinicalTrials.gov daily and presents it through an interactive web interface hosted on GitHub Pages.

## Features

- **Auto-refreshing data** via GitHub Actions (daily)
- **LLM-powered therapy area classification** using Claude Haiku (with smart caching to minimize cost)
- **Interactive dashboard** with search, multi-select filters, sortable table, sponsor cards, and analytics charts
- **Keyword fallback** — works without an API key using built-in keyword classification

## Quick Setup (5 minutes)

### 1. Create the GitHub Repository

1. Go to [github.com/new](https://github.com/new)
2. Name: `clinical-trials-dashboard` (or your choice)
3. Set to **Public**
4. **Do NOT** initialize with README (you'll push this package)
5. Click **Create repository**

### 2. Get an Anthropic API Key (for LLM classification)

1. Go to [console.anthropic.com](https://console.anthropic.com/)
2. Sign up or log in
3. Go to **API Keys** → **Create Key**
4. Copy the key (starts with `sk-ant-...`)
5. Add $5 credit (Settings → Billing) — this covers months of daily refreshes

> **Cost estimate:** First run classifies ~38,000 trials ≈ $3-5. Daily incremental runs classify only ~100-200 new trials ≈ $0.02-0.05/day.

### 3. Add the API Key to GitHub

1. In your new repo, go to **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name: `ANTHROPIC_API_KEY`
4. Value: paste your API key
5. Click **Add secret**

### 4. Push the Code

```bash
cd clinical-trials-dashboard
git init
git add -A
git commit -m "Initial commit: clinical trials dashboard"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/clinical-trials-dashboard.git
git push -u origin main
```

### 5. Enable GitHub Pages

1. Go to **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: `main` / `/ (root)`
4. Click **Save**

### 6. Trigger the First Data Refresh

1. Go to **Actions** tab
2. Click **Daily Clinical Trials Refresh** workflow
3. Click **Run workflow** → **Run workflow**
4. Wait ~15-20 minutes for the first full fetch + classification

### 7. View Your Dashboard

After the workflow completes, visit:
```
https://YOUR_USERNAME.github.io/clinical-trials-dashboard/
```

The dashboard auto-loads the latest data. It refreshes automatically every day at 6:00 AM UTC.

## File Structure

```
├── .github/workflows/daily-refresh.yml   # GitHub Actions: daily data refresh
├── data/
│   └── trials_data.json                  # Auto-generated trial data (committed by Actions)
├── classification_cache.json             # LLM classification cache (committed by Actions)
├── fetch_trials.py                       # Data fetcher + classifier + HTML generator
├── index.html                            # Dashboard viewer (auto-loads data)
├── requirements.txt                      # Python dependencies
└── README.md                             # This file
```

## Manual Usage

You can also run the script locally:

```bash
# Install dependencies
pip install -r requirements.txt

# Fetch data with LLM classification
export ANTHROPIC_API_KEY=sk-ant-...
python fetch_trials.py --json data/trials_data.json

# Fetch with keyword-only classification (no API key needed)
python fetch_trials.py --json data/trials_data.json --no-llm

# Quick test (1 page = ~1000 records)
python fetch_trials.py --json data/trials_data.json --test

# Regenerate the viewer HTML (only needed if you modify the template)
python fetch_trials.py --viewer index.html
```

## Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| `--days` | 365 | Lookback window (days from today) |
| `--max-pages` | None (all) | Cap API pages (1 page = 1000 trials) |
| `--use-llm` | Auto | Force LLM mode |
| `--no-llm` | False | Force keyword-only mode |
| `--cache-path` | `classification_cache.json` | LLM results cache |

## Sharing with Your Team

Share the GitHub Pages URL — anyone with the link can view the dashboard. No login required for public repos.

For your team's workflow:
1. **Dashboard viewers** → Just open the URL
2. **Lead generation** → Use the "Download Sponsors" button to export a deduplicated list for SeamlessAI
3. **Custom analysis** → Download `data/trials_data.json` for offline analysis
