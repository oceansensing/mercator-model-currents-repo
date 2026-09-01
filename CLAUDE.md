# mercator-model-currents-repo

Mercator ocean currents — **the horizontal currents from Mercator Ocean's global 1/12 deg analysis and forecast, at every depth this map publishes** — published as the map data
contract's grid files.

**Nothing runs yet.** This repository holds its rules, its plan and its
decisions; there is no `pipeline/products.toml`, no workflow, and nothing
published. What follows describes the intended shape and says where it is
intent rather than fact.

<!-- DOC-DOCTRINE v1 begin — identical in all ten repositories; `check:docs` holds them equal. Edit one, sync all. -->
## Where truth lives, and what "update docs" means

Ten repositories carry this project. The engine and the site:
`oceanlet.js`, `oceansensing.github.io` (the site, and every fetch script).
The orchestrator and the observations: `realtime-data-repo`. And the data
repositories, which since 2026-08-30 split **currents from fields** per model:
`espc-model-repo` (the ESPC currents — a legacy name, see below),
`espc-model-fields-repo`, `eccofs-model-currents-repo`,
`eccofs-model-fields-repo`, `mercator-model-currents-repo`,
`mercator-model-fields-repo`, and `sentinel3-data-repo` (ocean color, which
has no vector half to split). Each document answers exactly one question.

**`espc-model-repo` is the ESPC CURRENTS repository** despite its name — the
one exception to the convention, kept because its URL is a live origin and
GitHub Pages does not reliably redirect a renamed project site. Read it and
`eccofs-model-currents-repo` as the same kind of thing.

*(`eccofs-model-repo` was RENAMED to `eccofs-model-fields-repo` on 2026-08-30,
not superseded — GitHub redirects the old name, which is why a rename was
free there and is not free for `espc-model-repo`: that one has published
bytes behind a Pages URL, and Pages does not redirect what the API does.)*

**All ten carry the same four documents, and since 2026-08-31 a gate holds
them to it** — `check:docs` requires a `DECISIONS.md` tracked in git in every
repository. The last two landed that day, the site's and
`realtime-data-repo`'s, reconstructed from records that already existed:
nothing was missing but the file, which is how the site went seven weeks
without one and `realtime-data-repo` eighteen days. **This block asserted
otherwise from the day it was written** — byte-compared in the eight places there were then, and
false in two of them, because a gate on a text is a gate on the text. What it
cost is measurable: the engine promotion's own rehearsal listed *"a dated
entry in this repo's decisions and oceanlet's"* as its ninth step, and the
half with nowhere to go was simply not written.

| file | answers | tense | it is stale when |
| --- | --- | --- | --- |
| `README.md` | what this is, how to run it | present | a reader types a command or trusts a number and is wrong |
| `CLAUDE.md` | what must not be got wrong here | imperative | the next session is about to repeat a mistake |
| `PLAN.md` | what happened, measured, and what is open | dated past | "why is it like this?" has no answer here |
| `DECISIONS.md` | which one-way door closed, and when | dated | a reversal would cost a migration and nothing says so |
| `docs/` | contracts, ledgers and the guide | present | it describes an interface, a divergence or a concept that has moved on |

**`docs/` is a first-class part of "all docs", not an appendix** — the owner
asked for that explicitly on 2026-08-28, and the reason is that these are the
documents everything else points AT. A frozen contract, a divergence ledger
whose rows are pinned by tests, a guide that introduces the model: each is
the thing a reader is sent to when the short answer will not do, so each is
the worst place for a claim that has quietly stopped being true.

**"Update docs" means a sweep of all eight repositories, not the one in hand.**
Docs are part of the change, never a follow-up and never a separate ask. Six
questions, asked of every repository the change touched:

1. Did a command, a path, a script name or a number a reader would type or
   trust move? → `README.md`
2. Did a rule, a trap, or a things-that-must-move-together change or come to
   light? → `CLAUDE.md`
3. Did something *happen* — a measurement, a defect, a yield, a mechanism, an
   open question opened or answered? → `PLAN.md`
4. Did a one-way door close — **or has one already recorded stopped being
   fully true**? → `DECISIONS.md`, in **every** repository the change
   touched. All ten carry one, so this is no longer the
   engine's question with seven exemptions; the amendment half is here
   because two entries needed one within a day of being written.
5. Did an interface, a deliberate divergence, or a concept the guide explains
   move? → the matching file under `docs/`
6. **Does a document in another repository now say something false because of
   this change?** → fix it there, in the same sitting.

**Question 6 is the one that gets missed, and it is why this block is
identical in ten places.** Measured 2026-08-28: one tile-tier measurement
falsified `espc-model-repo`'s README, its `products.toml` header and the
site's README at once. Two were found; the third took a reminder from the
owner, who then asked for this doctrine.

**Two repositories are deliberately NOT in the list above, on opposite
grounds, and both are named because an exclusion nobody wrote down is
indistinguishable from an oversight.**

`ocean-now`, the iOS port, **consumes this system** — it mirrors the site's
published contract. It is not swept by these six questions and does not carry
this block; it has a lighter mechanism instead, a pending list in its parity
ledger, and the two repositories whose changes can reach it (the engine and
the site) each say so in their own section. It is named here because "four"
was read as "all of them" for two weeks while that ledger drifted 176 commits
behind with nothing noticing — question 6 failing at the granularity of a
whole repository rather than a document.

`hab-data-repo` is excluded on the opposite ground: **it does not touch the
ocean map at all** (the owner's call, 2026-08-31). It publishes the bloom
photographs for a different part of the website, reached through `HAB_DATA`
in `src/config.ts`, and carries no interface anything here codes against
beyond a URL and a filename convention. It needs no mechanism, not even a
lighter one — nothing in these ten can falsify a claim in it, and it cannot
falsify one here. Do not mix it in.

Adding a repository to the list above is therefore a real act: it buys the
sweep, and leaving one off **silently** costs exactly what `ocean-now` cost.

A number in prose is only as good as its anchor. `check:docs` gates every
claim it can tie to a source constant and nothing else, so when a figure has
no anchor — a measurement, a live reading, a byte count off a build log —
write **where it was measured and when**, or the next reader cannot tell a
fact from a guess that aged.
<!-- DOC-DOCTRINE v1 end -->

## What must not be got wrong here

**This is the currents half of Mercator, and the split is the convention
rather than an accident.** `espc-model-repo`'s PLAN set it for every model
added after ESPC: `<model>-model-currents-repo` and
`<model>-model-fields-repo`. The axis that costs bytes is vectors against
scalars — ESPC's currents are 738.7 MB of tiles against 169.5 MB of fields —
so the two halves have different storage budgets and different failure modes,
and sharing a publish gate would let one hold back the other.

**The upstream is Zarr behind a toolbox, not OPeNDAP, and none of the ESPC
fetch code carries over.** Copernicus retired OPeNDAP, ERDDAP, MOTU, FTP and
WMS in April 2024. There is no stdlib route: `copernicusmarine` is a required
dependency, and it takes a **username and password** — there is no API key,
whatever a search suggests. The pair lives as the two repository secrets
`COPERNICUSMARINE_SERVICE_USERNAME` and `_PASSWORD`.

**THE CHUNK SHAPE DECIDES WHETHER A DATASET IS USABLE AT ALL**, and it cost
two wrong answers to learn. Measured 2026-09-01: in this product family the
**hourly** datasets are chunked as time series — 3,648 time steps inside a
16x16 pixel tile, about 34,442 chunks for one global frame, nearly all of it
discarded — while the **daily and 6-hourly** ones are chunked as maps, 8
chunks a frame. One hourly sea-level frame took 866 s; its hourly replacement
ran 21 minutes and was still going when CI killed the run. Both times the
answer was in `preferred_chunks`, which is free metadata.

**So: ask the chunk shape before paying for a frame.** A dataset whose chunks
span time is built for pulling a long series at one point and is the wrong
shape for a map however fast the link is. No retry budget improves it.

**This repository is PUBLIC and the site repository is not.** The toolbox
announces which account it logged in as, so anything running here writes that
into a public log unless it masks it. Mask the username and any email-shaped
string, and gate it rather than remember it.

**The depths are not ESPC's depths.** Against ESPC's 0, 50, 200, 350 and
1000 m, the nearest Mercator levels are 0.49, 47.37, 186.13, 380.21 and
1062.44 m. Close enough to compare, far enough that 380 against 350 is 9%
deeper — a reader must be told rather than left to assume the labels mean the
same water.

**Do not pin the trailing dataset version.** Copernicus versions ids with a
date suffix (`_202406`) and retires them on a schedule, so a hardcoded one
ages; the toolbox warns about it and prefers `dataset_version`.

## Where truth lives here

- `README.md` — what this publishes and how to run it
- `CLAUDE.md` — this file: what must not be got wrong
- `PLAN.md` — what happened, measured, and what is open
- `DECISIONS.md` — which one-way door closed, and when
