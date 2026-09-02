# Decisions

Dated, irreversible-leaning decisions, one entry each, newest last. The
reasoning lives in `PLAN.md`; this file is the index of what was decided and
when, so a future reader never re-derives whether a door was walked through.

**What counts as one-way here**, because a data repository's doors are not an
engine's. Three shapes: a decision that puts bytes in readers' hands under a
shape they will code against; a decision about which repository owns a
product, since moving one costs a migration in two places; and a decision
that forecloses an upstream. Tuning a threshold is none of those.

## D1 — 2026-09-01 — Mercator is a PEER model, not a fallback for ESPC

The owner's call, settling a question the site's PLAN left open on
2026-08-03.

Both models are fetched every run and the reader chooses between them, rather
than Mercator being fetched only when ESPC has nothing newer. That discards
the fallback design recorded on the site — including its "fall back as a set,
never currents from one model beside SST from another" constraint — because
nothing falls back: there are two independent forecasts, each labelled with
the `source` and `modelRun` the contract already carries and the map already
shows.

One-way in the first sense above. A peer publishes bytes continuously under
its own roots, and readers code against them; withdrawing it later is a
migration in the site and in every link that named it.

## D2 — 2026-09-01 — Two repositories, currents and fields, per the convention

`mercator-model-currents-repo` and `mercator-model-fields-repo`, following
the shape `espc-model-repo`'s PLAN set for every model added after ESPC.

Measured rather than assumed: the axis that costs bytes is vectors against
scalars. ESPC's currents are 738.7 MB of tiles — 89% of that repository's own
bytes — against 169.5 MB for its five scalar fields. Two halves in one
repository would put a 1 GB Pages cap in reach of a single cloudy fortnight
of tiles, and would make one half's outage hold back the other's publish.

One-way in the second sense: moving a product between repositories is cheap
in machinery and expensive in everything that points at it — roots in the
contract, origins on the site, and the union `check:docs` holds.

## D3 — 2026-09-01 — Five depths, two leads, and names that carry the depth

The set count, decided from the measurement `PLAN.md` records rather than
copied from ESPC's ten sets.

**Five depths** matching ESPC's as closely as this grid allows, so the two
models can be put side by side, and **two leads** -- parity with ESPC rather
than the +9 days Mercator offers. Twenty frames a build, about 672 MB off the
service and 4.9 MB published. Depths are priced individually (five levels cost
about five times one, measured), so a set costs what its levels cost.

**A starting shape, not a ceiling.** Leads are the cheap axis to grow and the
upstream carries far more of them; depth is the expensive one, a frame per
level per lead per component.

**And the roots are named for the water they hold**: `-380m` carries 380.21 m,
not ESPC's 350. Naming it `-350m` so the two models' filenames lined up would
have written "same word, different water" into the contract itself, and the
map's layer names follow the roots for the same reason.

*Amended 2026-09-01, the same day.* The **three deepest point depths became
depth AVERAGES** before the set ever published: `-186m`, `-380m` and `-1062m`
were point levels where ESPC publishes 0-200, 0-350 and 0-1000 m means, so
they became `-avg200m`, `-avg350m` and `-avg1000m` -- a mean over the column
to the depth in the name, not a reading at it. Two point levels remain, 0.49
and 47.37 m. The naming rule stands and gained an instance the same evening:
`temp29-mercator` carries the model's 29.445 m, not ESPC's 30.

One-way in the first sense this file names: resolution, depth and lead are the
shape readers code against. Dropping a depth strands anything built on it, and
renaming one silently changes which water a filename promises.

## Open, and not decided here

The **set count** — how many depths and how many leads. Mercator's currents
are 6-hourly with forecast to +9 days, against ESPC's two leads, so this is a
choice with a price on it rather than a copy, and `PLAN.md` carries the
measurement it will be made from. Also open: the published resolution and
extent, and whether the two halves share a cadence.
