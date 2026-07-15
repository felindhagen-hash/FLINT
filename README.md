# FLINT
# The Holding — economy simulator

Working design lab for **"Flint"** (working title), an unannounced iron-age
village management game by **Tessellum Games**. This is not the game — it is
the balance simulator used to design its economy: a fast-forwardable model of
workers, crews, task queues, travel, resource sites, tool wear, and
survival pressure, with every number editable at runtime.

**Play it:** https://REPLACE-ME.github.io/REPLACE-ME/ *(GitHub Pages, works on phone)*

## What's in here
| File | Role |
|---|---|
| `index.html` | The entire simulator — single file, zero dependencies, runs anywhere |
| `CLAUDE.md` | Implementation brief and binding design locks for AI-assisted sessions |
| `data/defaults-v04.json` | Canonical numbers baseline (v0.4, 2026-07-15) |

## How it works
- Time advances in simulated hours (+1h to +7 days). Twelve hours of daylight,
  twelve of night. Workers eat by day; fires burn wood by night.
- Crews follow **looping task queues** — trips to named sites (walk out, fill
  to carry capacity, walk home by dusk) and work blocks at the holding.
- Sites deplete: organics regrow at dawn, minerals never do.
- Nothing is balanced on purpose. Playtesting sets the numbers; the
  **Export data** button produces the canonical JSON, committed to `data/`.

## Status
Active design exploration. Versions are commits, the git log is the balance
changelog, and the design document lives elsewhere. Issues and PRs are not
being taken — this repo is public for hosting convenience, not collaboration.

© Tessellum Games. All rights reserved. Public visibility is not a license;
no permission is granted to reuse, redistribute, or build upon this code or design.
