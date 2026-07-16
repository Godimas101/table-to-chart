# AGENTS.md — table-to-chart

GitHub Action that watches specific markdown files for a table + comment marker, then generates + commits an SVG chart of that table. Used by `personal-projects/health-tracking/` to auto-render weight trend charts.

## What this is

GitHub Action that watches specific markdown files for a table + comment marker, then generates + commits an SVG chart of that table. Used by `personal-projects/health-tracking/` to auto-render weight trend charts.

## Where work lives (RULE — non-negotiable)

**Every task on this repo is a ticket on the [Personal Projects board](https://github.com/users/Godimas101/projects/2).** YOU (the agent) create the ticket BEFORE touching anything. No exceptions for "small" work.

Concrete rules — same as everywhere:

- **Starting work?** Open a ticket, add to the board, set Status = **In Progress**, then start.
- **Have an idea for later?** Ticket in **Backlog**. Not in memory, not in a README, not in NOTES.md.
- **Need Chris to check something before closing?** Move to **In QA** and comment what he needs to look at. Do NOT set to Done — that's Chris's call after review.
- **Finished + verified yourself?** Close the ticket with a closing summary (what you did / problems + solutions / anything NOT done).
- **Same-session micro-work?** Open + close in the same session — but the ticket exists.
- **Older than 30 days in Done?** The weekly cron moves it to Archived. The closed ticket persists.

Ticket body shape: see memory `[[feedback-ticket-body-shape]]` — What/Why → Acceptance → Related → Notes. Priority defaults to P2, Kind defaults to Feature.

## How to verify (before flagging In QA or closing)

- Test in a fork with the action installed — edit a table, push, verify the SVG gets committed back.
- If touching the chart-rendering: try tables with edge cases (single row, non-numeric columns, empty cells).
- Verify SVG output doesn't overflow — some markdown tables produce very wide charts.

## MUST NOT

- Break backwards compatibility of the comment-marker syntax — Chris has existing tables using it in personal-projects.
- Push the SVG with `[skip ci]` missing — that would cause infinite Actions loops.

## Related

- Used by: [`personal-projects/health-tracking/`](https://github.com/Godimas101/personal-projects/tree/main/health-tracking) (weight trend chart)
- Fed data by: [`automatic-weight-recording`](https://github.com/Godimas101/automatic-weight-recording)

---

*Part of Chris's `Godimas101` personal repos. Companion guide: `personal-docs/git-infrastructure.md` (private companion repo) covers the full infrastructure.*