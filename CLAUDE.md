# CLAUDE.md — Holding Economy Simulator (working title "Flint")

## What this repo is
The **balance simulator and design laboratory** for an iron-age village
management game (Tessellum Games / Fredrik). Design discussions and verdicts
happen in a separate Claude project; the GDD lives in Notion. This repo owns
**implementation only**. Do not invent design; when a task conflicts with a
lock below, stop and flag it instead of resolving it silently.

## Hard guardrails
- Single self-contained HTML file (`index.html` = the sim). No build step,
  no framework, no npm, no external requests. Must run from file:// and
  GitHub Pages, on phone-width screens. https://felindhagen-hash.github.io/FLINT/
- **No Godot project in this repo, ever.** The game build is a separate,
  ungated-later decision.
- All game data lives in the one `DEFAULT_DATA` block; engine logic reads
  only from it. Every number editable at runtime. Playtest determines values.
- Persistence: `window.storage` adapter with silent fallback to Export/Import
  (the adapter exists only for Claude-artifact runs; on Pages it degrades
  gracefully). Exported JSON is the canonical numbers spine — keep export
  pretty-printed and key-stable (diff-friendly).
- Incremental edits only. Never regenerate the file from scratch.

## Current state: v0.4 (2026-07-15) — feature inventory
Hourly tick sim, 12h day / 12h night. Crews with **cyclic task queues**
(site trips + holding-task hour-blocks; crews skip unworkable entries).
Discrete trips: walk out (dist/speed) + fill (capacity·size ÷ gather rate)
+ walk back; **home-by-dusk truncation** (refuse only when round-trip walking
alone exceeds remaining daylight). **Sites** with stock/cap/regrow —
organics regrow at dawn, minerals (regrow 0) deplete forever. Crafting
consumes worker-time via an order queue worked by crews with a Crafting
entry. Tool wear pools with breakage. Soft-fail: hunger/cold →
efficiency penalty next day (settings.penaltyMode: 0 = 50% cliff,
1 = proportional to shortfall), nobody ever dies. Hut → delayed arrival,
joins crew 1. Deadlock detector (reachability incl. exhausted sites).
Instruments: night-forecast header, per-day labor ledger
(upkeep/progress/idle %), full-resource day summaries, trip deliveries
logged with cargo, ★ milestone stamps. Import migrates v0.3 exports
(dist-activities → auto-sites, string assignments → queues).

## Design locks that bind implementation (from the GDD)
1. **Soft-fail only.** Neglect slows the holding; never kills, never softlocks.
2. **Deterministic.** No RNG anywhere yet. Future injuries are a bounded,
   telegraphed design — do not add randomness ad hoc.
3. **Everything upfront.** Costs, durations, risks, forecasts shown before
   commitment. New mechanics must surface their numbers in the UI.
4. **Earned idle + solvency law.** A four-site rotation (glade → copse →
   heath → hollow) must keep the holding solvent at casual check-in
   cadence; attention buys speed (~1.5–2×), never survival. Acceptance
   test: four-site rotation queue, +7 days, hands off, undegraded. Run
   this test after any economy-touching change. The two-entry starter
   queue (glade/copse) is intentionally lighter and may degrade over a
   long run — rotating to more sites is the intended player response,
   not a bug. Regrow law (amended v0.4.3): 20–30% of one worker's daily
   harvest per site; a site visited every k days drains only if
   regrow < harvest/k.
5. **Day/night cycle is load-bearing** (warmth pressure, dusk deadlines,
   trip quantization). Never remove or bypass it.
6. **Sites are data rows, never map coordinates.** No map, no pathing.
7. **No universal currency, ever.** Goods are use-values.
8. Overview-first UI: detail as overlays/sections, crises as home-screen
   state changes, never popups.

## Roadmap (do not build ahead of the current milestone)
- **v0.5 — lifecycle laboratory:** per-worker identity (names, ~50/50 gender,
  cosmetic), expertise via solo repetitions (crew members don't learn),
  crews formally unlock with first expert, career durability in field
  work-hours → elder (teacher 2× training XOR steward, permanent choice),
  elder mortality as telegraphed lifecycle completion → hall of fame.
  Dials: training time T, teacher factor k, elder span E, career length C,
  run-length target. Sustainability law to verify in-sim: E ≥ kT/(1−s).
  Per-generation report: experts minted vs expired, skill trend.
- **v0.6 — check-in cadence mode:** freeze assignments, auto-run N hours,
  to measure the real (non-perfect-attention) player experience.
- Later, per GDD sequencing: tents/field-self-sufficiency, farms/stewards,
  pens (hens/rabbits only), medicine. Not before their gates.

## Testing practice (keep it)
Headless smoke tests: extract the script
(`sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d' > sim.js`),
stub `document` with minimal element mocks, force `hasStorage=false`,
append test scenarios inside the evaluated source (top-level `let/const`
don't cross eval scope), run with node. Always re-run the frozen-week
solvency test (lock 4) plus a depletion-skip and a hunt+cook rhythm check
after sim changes. Test in clean order — imports mutate global data.

## Workflow protocol
- Design verdicts arrive from the design chat / Notion GDD as short spec
  blocks. Implement exactly; flag conflicts with locks rather than resolving.
- Numbers flow via exported JSON: Fredrik tunes in the live sim, exports,
  commits as `data/*.json`; fold confirmed tunings into DEFAULT_DATA.
- Commit per feature, message says what changed in the *economy or UI*,
  not just the code. The git log is the balance changelog.
- Keep GitHub Pages deployment current — the live URL is the phone playtest
  surface and the design chat fetches it. https://felindhagen-hash.github.io/FLINT/
