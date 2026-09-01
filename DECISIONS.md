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

## Open, and not decided here

The **set count** — how many depths and how many leads. Mercator's currents
are 6-hourly with forecast to +9 days, against ESPC's two leads, so this is a
choice with a price on it rather than a copy, and `PLAN.md` carries the
measurement it will be made from. Also open: the published resolution and
extent, and whether the two halves share a cadence.
