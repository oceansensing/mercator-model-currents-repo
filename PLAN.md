# mercator-model-currents-repo — the founding plan and running record

The currents half of a Mercator Ocean peer to ESPC. **Nothing runs yet**: this
records what was measured before anything was built, and what is still open.

## Where it stands

**It publishes, since 2026-09-01.** `pipeline/products.toml` declares the
products, the workflow builds them, and the schedule is on at
`13 1,7,13,19` -- four runs a day.

The fetch path is `scripts/fetch-mercator.py` in the site repository, and it
was validated against the live service before any of this was switched on:
a scalar frame came back at 360x171, 1.0 deg, -2.093 to 35.76 degrees C, and a
currents frame at +/-1.75 m/s with wet counts falling and speeds weakening
with depth. Numbers that would not survive a transform fault.

**The probe is what makes a schedule affordable.** A full run here is
20 frames, about 12 minutes; the probe reads the upstream's time axis -- metadata, about 5 s --
and compares it against the `refTime` on disk, so a run with nothing new costs
seconds. It also rebuilds when this script would publish something the disk
does not have, because a probe watching only the upstream cannot see a change
in us.

Created 2026-09-01 with its four documents and its credentials. The deploy key that lets a pipeline here read the private site
repository is on that repository as `mercator-model-currents-repo-checkout`,
read-only; the Copernicus pair and `PIPELINES_SSH_KEY` are secrets here.

**The set count was decided and is no longer blocking**: five depths matching
ESPC's as closely as this grid allows, and two leads -- parity with ESPC
rather than the +9 days on offer. A starting shape, not a ceiling: leads are
the cheap axis to grow and depth is not.

## Why a second model, and why this one

ESPC is flaky in two unrelated ways and only one is handled. Per-request
failures are covered by retries. The other is that **the run itself goes
late** — and on 2026-08-31 it went further than late: `tds.hycom.org` refused
every connection for hours, all five ESPC products were briefly held, and
every ESPC tile tier was withdrawn from the live map, so a reader panning
west of -100 saw the 0.96 deg globe instead of 0.08 deg detail. No amount of
retrying fixes that. Only a different model does.

Mercator is the only like-for-like option: the same 1/12 deg, with currents,
temperature and salinity from one run. The alternatives have thinned — NOAA
retired NOMADS' OPeNDAP in 2025, and OSCAR on CoastWatch ERDDAP has been
stale since 2014-10-06. The site's `PLAN.md` carries that survey.

## The upstream, verified 2026-09-01

| | |
| --- | --- |
| product | `GLOBAL_ANALYSISFORECAST_PHY_001_024` |
| dataset | `cmems_mod_glo_phy-cur_anfc_0.083deg_PT6H-i` |
| variables | `uo` and `vo` |
| grid | 2041 x 4320 at 0.083 deg |
| cadence | 6-hourly |
| depths | 50 levels, 0.49 m to 5727.92 m |
| access | `copernicusmarine` 2.4.1 over Zarr, account required |

**There is no API key.** The toolbox takes a username and password; every
documented form is that same pair in a different wrapper. OPeNDAP, ERDDAP,
MOTU, FTP and WMS were all retired in April 2024, so there is no route the
stdlib could take — which makes the dependency unavoidable rather than a
preference. It installs in 22 s and imports in 1.8 s, so the cost the site's
plan worried about in August is small, and it is no longer a first: three
pipelines `pip install` already.

## What was measured, 2026-09-01

| | measured |
| --- | --- |
| one global frame | **33.6 MB** float32 |
| transfer | 0.9 MB/s (36 s a frame) |
| five depths against one | about 5x — **linear** |
| toolbox install | 22 s |

**Every transfer figure above was measured through the toolbox's DEFAULT dask
blocks, which read about 26x the bytes a level needs** (found 2026-09-01: 50.7 s
a level that way, 1.9 s with `chunk_size_limit=0`, on the same store). They
were true of a path the build no longer takes. The fetcher opens with no dask
now and refuses a dask-backed array; the site's `PLAN.md` carries the
measurement and the runner's own numbers are in the run logs, timestamped per
line.

**Depths are priced individually**, which the `depth: 1` chunking predicts
and the timing confirms: currents spent about 4 minutes on five levels
against 36 s for one, temperature 65 s against 9 s. So a set costs what its
levels cost, and ESPC's per-depth instinct carries over even though its
access method does not.

### The chunk shape decides usability, and it is free to ask

The most useful thing learned, and it cost two wrong answers.

| dataset | chunks | one global frame |
| --- | --- | --- |
| currents, 6-hourly | `time 1, lat 512, lon 2048` | 8 chunks |
| temperature / salinity, daily | `time 1, lat 512, lon 2048` | 8 chunks |
| all-variables, **daily** (`zos`) | `time 1, lat 512, lon 2048` | 8 chunks |
| sea level, merged, **hourly** | `time 3648, lat 16, lon 16` | **34,442 chunks** |
| all-variables, **hourly** (`zos`) | `time 3216, lat 16, lon 16` | **34,442 chunks** |

**In this family the hourly products are chunked as time series and the daily
and 6-hourly ones as maps.** A 16x16 pixel tile holding 3,648 time steps is
built for pulling a long series at one point; a global frame off it reads
about 34,442 chunks and discards nearly all of them. That is a property of
the store, not a performance problem.

It was learned twice, expensively: `merged-sl` took **866 s** for one frame,
and the hourly replacement reached for next — by reasoning from the other
datasets rather than asking — ran **21 minutes** and was still going when the
30-minute job cap killed the run. Both times the answer was sitting in
`preferred_chunks`. `scripts/probe-mercator-chunks.py` in the site repository
now asks it in **27 seconds**, and carries the known-good datasets as a
positive control, because a probe that only ever prints "bad" cannot be
trusted to notice.

**Sea surface height therefore comes from the DAILY all-variables product.**

## The tile tier

**20 degree boxes at native 1/12 deg** -- exactly 240 cells a side, which is
why the box is 20 and not 15 -- matching the ESPC currents so the two models
tile alike and a reader zooming in makes the same shape of request of either.

**It costs nothing extra upstream.** The fetch already pulls the full native
global frame in order to decimate it for the 1 degree overview; a tile is a
slice of that same array. What it costs is published bytes and the time to
serialise them.

**One tier per depth AND per lead.** A forecast frame pointing at lead 0's
tiles would draw the present at 1/12 deg and call it the forecast -- sharp,
plausible and wrong, which is the failure that looks most like success. The
contract refuses a lead file whose `tileIndex` is not its own set.

Three faults were found building it, each by a check rather than by a reader:

- **Rows overlapped by one cell.** A row holds the cells whose center lies in
  [s, s+20) and latitude descends, so the start is `floor(..) + 1`, not
  `ceil` -- which returns the boundary itself when the division is exact, and
  it is exact at every 20 degree edge on a 1/12 degree grid. 2049 rows
  covering a 2041-row grid.
- **Two lattice mutations SURVIVED the first test**, both for one reason: with
  this grid's `lo1 = 0` every box edge divides exactly, so `floor` and `ceil`
  agree everywhere. The lattice now derives its origin from the grid rather
  than from a constant, and the test carries a second geometry whose origin is
  not on a box edge.
- **The grid-step guard read float32 noise as a re-grid.** Coordinates are
  stored as float32, whose precision near 180 degrees is about 1.5e-5, so
  differencing two adjacent longitudes returns 0.0833282470703125 for a grid
  that is exactly 1/12. Derived from the whole span the error divides by 4,319
  intervals and the step comes back to 1.2e-9. Reproduced locally before the
  tolerance was touched.

And one that was not the tier at all: **the `--tile-key` probe runs in `plan`,
which had no Copernicus credentials**, so every product reported "tiles
unplanned this run" -- a message naming the tiles rather than the cause.

Caches are per product. The key is computed per product, so products sharing
one cache name would each save over the others and every restore would be
somebody else's tier.

## Open

1. **The set count** — how many depths, how many leads. An ESPC-shaped set
   (five depths x two leads x two components) is 20 frames and **672 MB**;
   *12.5 minutes a build* was its price on the slow path and is not its price
   now. Mercator offers forecast to +9 days against ESPC's two leads, so this
   is a choice with a price rather than a copy.
   **Do not size it from ESPC's 738.7 MB**: that number was measured against
   OPeNDAP, which charges for different things than Zarr does.
2. **Published resolution and extent** — waiting on a byte measurement of the
   output, the way the ocean color tier was.
3. **Whether the two halves share a cadence.** Currents are 6-hourly and the
   scalars daily upstream, so a shared publish hour is a decision, not a
   given.
4. **How the map names two models' layers** so a reader is never guessing
   which ocean is on screen — including that the depths differ: 380.21 m here
   against ESPC's 350 m is 9% deeper.

## Method note

Every number above has a run behind it, taken against the live service on
2026-09-01 from `scripts/measure-mercator.py` and
`scripts/probe-mercator-chunks.py` in the site repository. The transfer rates
are one sample each on a GitHub runner and will move with the day; the chunk
shapes are properties of the store and will not.

## 2026-09-01 — the caps reached the wrong depth, and the roots were shuffled

Three defects, none of which reached this tree: the build that would have
published them was cancelled on its timeout first. All three would have
deployed cleanly, which is why they are written down.

**The caps did not integrate to their own names.** A level was counted for its
whole layer whenever its centre sat above the cap, so the integral ran to the
midpoint below the deepest included level and divided by that. Measured on the
real profile:

| root | promises | covered, before | covered, now |
| --- | --- | --- | --- |
| `cur-mercator-avg200m` | 200 m | 270.301 m | **200.000 m** |
| `cur-mercator-avg350m` | 350 m | 386.033 m | **350.000 m** |
| `cur-mercator-avg1000m` | 1000 m | 902.339 m | **1000.000 m** |

The 200 m cap was averaging 35% more water than its name claimed, and the
figure **moved with `PROFILE_STRIDE`** — 189 m at stride 2, 211 m at stride 3
— so a performance constant was silently redefining a published quantity. The
deepest layer is clipped to the cap now, and the profile reads one level past
the deepest cap (1062.44 m) so the 1000 m cap has a layer to clip rather than
stopping at its last sample. Cost: one extra level read per component per lead.

**Five point levels were planned against two point roots.** The product spec
still named all five ESPC-like depths as point levels after the set was cut to
two, so eight frames were planned against five declared roots and the write
loop's `else roots[0]` fallback sent the three overflow frames to
`cur-mercator.json` one after another. Published, that would have put the
0–1000 m mean in the surface root and point velocities at 186/380/1062 m into
the three `-avg*` roots — every currents root but `-47m` mislabeled. The tell
was three consecutive writes of the same filename at 487, 479 and 471 KB in a
build log.

**Neither was visible to the suite.** The roots list was checked; the plan that
fills it was not. Un-clipping the deepest layer left everything green. Both now
have pins that separate the two arithmetics, each mutation-tested.

**The depth axis is now known rather than assumed.** Printed off the live
service by the site's `scripts/probe-mercator-chunks.py`: **50 levels**, 0.494
to 5727.917 m. 22 of them sit in the top 100 m; only 36 are at or above
1062 m, so a 0–1000 m cap never touches the abyssal 14. The stride comparison
against full resolution is in the site's `PLAN.md` — stride 2 is kept, and a
hand-picked 18-level set was measured worse at every cap.
