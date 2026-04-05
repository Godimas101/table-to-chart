<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=slice&height=180&color=0:0B1021,45:0EA5E9,100:7C3AED&text=table-to-chart&fontColor=ffffff&fontSize=38&fontAlign=67&fontAlignY=28&rotate=11&desc=markdown%20table%20%E2%86%92%20SVG%20chart%20%E2%86%92%20auto-committed%20to%20your%20repo&descAlign=57&descAlignY=50&descSize=16" />
</p>

> **"Your data is already in the table. The chart should write itself."**

A GitHub Actions tool that reads any date-indexed markdown table, generates rolling-window SVG charts, and commits them back to your repo automatically. Configure which columns to plot, drop in two files, embed an image tag — done. Run the script multiple times in one workflow to generate multiple charts from the same table.

---

## 🚀 What It Does

- **Works with any markdown table** — point it at the date column and the columns you want to plot
- **Multiple charts from one table** — run the script multiple times in the same workflow, each with different column configs and output files
- **Multiple lines per chart** — primary metric on the left axis, unlimited secondary metrics on the right
- **Configurable rolling window** — last N months, set per-repo or overridden at run time
- **Dark-themed SVG** — renders cleanly in GitHub's light and dark modes
- **Linear trendline** — optional, on by default for the primary (left) column
- **Auto-commits all charts** — workflow pushes the updated SVGs after every relevant push
- **Handles sparse data** — missing values, partial rows, and timestamps in date cells all work

---

## 📋 Table Requirements

A standard markdown table with at least one date column and one numeric column. Columns can be in any order — you specify which ones to use by name or index.

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

Files with multiple tables (e.g. a glossary above the data) are handled automatically — the script searches all tables for the one containing your date column.

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

**3. Embed the charts** in your markdown file wherever you want them to appear:

```markdown
![Chart](charts/chart.svg)
```

**4. Push** — the workflow triggers, generates the SVGs, and commits them back. GitHub renders them inline.

---

## ⚙️ Configuration

All configuration is via environment variables set in the workflow file.

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
| `Y1_COL` | `1` | Primary (left) y-axis column — trendline is drawn on this |
| `Y2_COLS` | *(blank)* | Comma-separated right-axis columns — leave blank to disable |

### Labels & Appearance

| Variable | Default | Description |
|---|---|---|
| `Y1_LABEL` | *(column header)* | Left axis label |
| `Y2_LABELS` | *(column headers)* | Comma-separated right axis labels, matching `Y2_COLS` order |
| `Y2_AXIS_LABEL` | *(blank)* | Label for the right y-axis itself |
| `TITLE` | *(auto)* | Chart title — defaults to `{Y1 column} — Last N Months` |
| `STRIP_CHARS` | `%` | Characters to strip from values before parsing (e.g. `%` turns `24.8%` into `24.8`) |
| `SHOW_TREND` | `true` | Draw a linear trendline on Y1 — `true` or `false` |

### Manual Runs

Trigger the workflow manually from the GitHub Actions tab to override `MONTHS` at run time — useful for experimenting with different window sizes.

---

## 📝 Example Configs

**Single metric with trendline:**
```yaml
DATE_COL:   'Date'
Y1_COL:     'Weight'
Y2_COLS:    ''
Y1_LABEL:   'Weight (lbs)'
TITLE:      'Weight'
SHOW_TREND: 'true'
```

**Multiple secondary lines:**
```yaml
DATE_COL:      'Date'
Y1_COL:        'Muscle'
Y2_COLS:       'Body Fat,BMI'
Y1_LABEL:      'Muscle %'
Y2_LABELS:     'Body Fat %,BMI'
Y2_AXIS_LABEL: '%'
TITLE:         'Body Metrics'
STRIP_CHARS:   '%'
```

**Step count with calories:**
```yaml
DATE_COL:    '0'
Y1_COL:      'Steps'
Y2_COLS:     'Calories'
Y2_LABELS:   'Calories'
TITLE:       'Daily Activity'
STRIP_CHARS: ''
```

---

## 📊 Multiple Charts from One Table

Run the script multiple times in the same workflow — each run gets its own env vars and output file. Embed multiple image tags in your markdown to display them all.

```yaml
- name: Generate weight chart
  env:
    INPUT_FILE:  data/my-data.md
    OUTPUT_FILE: data/charts/weight-chart.svg
    Y1_COL:      'Weight'
    Y2_COLS:     ''
    TITLE:       'Weight'
  run: python data/generate_chart.py

- name: Generate metrics chart
  env:
    INPUT_FILE:  data/my-data.md
    OUTPUT_FILE: data/charts/metrics-chart.svg
    Y1_COL:      'Muscle'
    Y2_COLS:     'Body Fat,BMI'
    Y2_LABELS:   'Body Fat %,BMI'
    TITLE:       'Body Metrics'
  run: python data/generate_chart.py

- name: Commit charts
  run: |
    git config user.name "github-actions[bot]"
    git config user.email "github-actions[bot]@users.noreply.github.com"
    git add data/charts/weight-chart.svg data/charts/metrics-chart.svg
    git diff --cached --quiet || git commit -m "chore: update charts [skip ci]"
    git push
```

Then in your markdown:

```markdown
![Weight](data/charts/weight-chart.svg)
![Body Metrics](data/charts/metrics-chart.svg)
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
