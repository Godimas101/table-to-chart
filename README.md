<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=slice&height=180&color=0:0B1021,45:0EA5E9,100:7C3AED&text=table-to-chart&fontColor=ffffff&fontSize=38&fontAlign=67&fontAlignY=28&rotate=11&desc=markdown%20table%20%E2%86%92%20SVG%20chart%20%E2%86%92%20auto-committed%20to%20your%20repo&descAlign=57&descAlignY=50&descSize=16" />
</p>

> **"Your data is already in the table. The chart should write itself."**

A GitHub Actions tool that reads any date-indexed markdown table, generates a rolling-window SVG chart, and commits it back to your repo automatically. Configure which columns to plot, drop in two files, embed an image tag — done.

---

## 🚀 What It Does

- **Works with any markdown table** — point it at the date column and the columns you want to plot
- **Configurable rolling window** — last N months, set per-repo or overridden at run time
- **Two-axis support** — primary metric on the left, optional secondary metric on the right
- **Dark-themed SVG** — renders cleanly in GitHub's light and dark modes
- **Linear trendline** — optional, on by default for the primary column
- **Auto-commits the chart** — workflow pushes the updated SVG after every relevant push
- **Handles sparse data** — missing values, partial rows, and timestamps in date cells all work

---

## 📋 Table Requirements

The tool needs a standard markdown table with at least one date column and one numeric column. Columns can be in any order — you specify which ones to use by name or index.

```markdown
| Date       | Steps | Calories | Sleep (hrs) |
|------------|-------|----------|-------------|
| 2026-01-01 | 8200  | 2100     | 7.5         |
| 2026-01-03 | 6400  | 1980     | 8.0         |
```

Dates with timestamps work too:

```
| 2026-03-03 06:43:50 | 211 | 27.0% |
```

---

## 🔧 Setup

**1. Copy the script** into the repo containing your markdown file:

```
your-repo/
  data/
    my-data.md          ← your existing markdown table
    generate_chart.py   ← paste here
    charts/
      .gitkeep          ← add this so git tracks the folder
```

**2. Copy `workflow-template.yml`** into `.github/workflows/` and fill in your paths and column config (see below).

**3. Embed the chart** in your markdown file wherever you want it to appear:

```markdown
![Chart](charts/chart.svg)
```

**4. Push** — the workflow triggers, generates the SVG, and commits it back. GitHub renders it inline.

---

## ⚙️ Configuration

All configuration is via environment variables in the workflow file.

### Paths & Window

| Variable | Default | Description |
|---|---|---|
| `MONTHS` | `6` | How many months back to include |
| `INPUT_FILE` | — | Path to your markdown file (relative to repo root) |
| `OUTPUT_FILE` | — | Where to write the SVG |

### Column Selection

Columns can be specified as a **0-based index** or the **exact header name** (case-insensitive).

| Variable | Default | Description |
|---|---|---|
| `DATE_COL` | `0` | Column containing dates |
| `Y1_COL` | `1` | Primary (left) y-axis column |
| `Y2_COL` | *(blank)* | Secondary (right) y-axis column — leave blank to disable |

### Labels & Appearance

| Variable | Default | Description |
|---|---|---|
| `Y1_LABEL` | *(column header)* | Left axis label |
| `Y2_LABEL` | *(column header)* | Right axis label |
| `TITLE` | *(auto)* | Chart title — defaults to `{column} — Last N Months` |
| `STRIP_CHARS` | `%` | Characters to strip from values before parsing (e.g. `%` turns `24.8%` into `24.8`) |
| `SHOW_TREND` | `true` | Draw a linear trendline on Y1 — `true` or `false` |

### Manual Runs

You can trigger the workflow manually from the GitHub Actions tab and override `MONTHS` at run time — useful for experimenting with different window sizes.

---

## 📝 Example Configs

**Weight + body fat from a health log:**
```yaml
DATE_COL: 'Date'
Y1_COL:   'Weight'
Y2_COL:   'Body Fat'
Y1_LABEL: 'Weight (lbs)'
Y2_LABEL: 'Body Fat %'
TITLE:    'Weight Tracking'
STRIP_CHARS: '%'
```

**Step count from a fitness log:**
```yaml
DATE_COL: '0'
Y1_COL:   'Steps'
Y2_COL:   'Calories'
TITLE:    'Daily Activity'
STRIP_CHARS: ''
```

**Single metric, no secondary axis:**
```yaml
DATE_COL: 'Date'
Y1_COL:   'Sleep (hrs)'
Y2_COL:   ''
SHOW_TREND: 'true'
```

---

## 📦 Requirements

| Requirement | Notes |
|---|---|
| GitHub repo with Actions enabled | Free on public and private repos |
| Python 3.10+ | Provided automatically by the `ubuntu-latest` runner |
| `matplotlib`, `numpy`, `python-dateutil` | Installed automatically by the workflow |

---

## 🙏 Credits

Built by **Chris Carpenter** and **Claude Sonnet 4.6**.

---

## 🧡 Support

This tool is free and always will be. If it saves you some manual chart-wrangling, consider supporting on Patreon — it helps keep the tools and mods coming.

[![Support on Patreon](https://raw.githubusercontent.com/Godimas101/personal-projects/main/patreon/images/buttons/patreon-medium.png)](https://patreon.com/Godimas101)

---

*Track the trend. Ignore the noise.*
