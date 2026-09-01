# mercator-model-currents-repo

Mercator ocean currents for the C4PO ocean map — **the horizontal currents from Mercator Ocean's global 1/12 deg analysis and forecast, at every depth this map publishes**.

**It publishes, since 2026-09-01.** `pipeline/products.toml` declares the
products, the workflow builds them four times a day, and the site draws
five current depths — 0, 47, 186, 380 and 1062 m under names that carry the model.

**The layer names carry the depth that is actually there**, not ESPC's
nearest: 47 m against 50, 186 against 200, 380 against 350, 1062 against
1000. Labelling them with ESPC's round numbers so the two lists lined up
would say the layers are comparable at the same depth when 380 against 350 is
9% deeper water.

`PLAN.md` carries the measurements and what is open. `DECISIONS.md` indexes
the dated one-way decisions. **Which document gets what, and what "update
docs" means across all ten repositories, is the doctrine block at the top of
`CLAUDE.md`** — the same text in all ten, held equal by the site's
`check:docs`.

## What it will publish

| | |
| --- | --- |
| source | Mercator Ocean International, via Copernicus Marine (CMEMS) |
| product | `GLOBAL_ANALYSISFORECAST_PHY_001_024` |
| dataset | `cmems_mod_glo_phy-cur_anfc_0.083deg_PT6H-i` |
| variables | `uo`, `vo` |
| resolution | **0.083 deg (1/12 deg)**, 2041 x 4320 global |
| cadence | 6-hourly, with forecast to +9 days |
| access | the `copernicusmarine` toolbox over Zarr; account required |

## What one frame costs, measured 2026-09-01

| | |
| --- | --- |
| one global frame | **33.6 MB** float32 |
| currents transfer | 0.9 MB/s (36 s a frame) |
| scalar transfer | 3.6 MB/s (9 s a frame) |
| depths | 50 levels, 0.49 m to 5727.92 m, priced individually |
| toolbox install | 22 s |

An ESPC-shaped set — five depths x two leads x (u,v) — would be 20 frames,
about **672 MB and 12.5 minutes a build**. Whether that is the right shape is
open: Mercator offers far more leads than ESPC's two, so the set count is a
choice with a price on it rather than a copy. `PLAN.md` has the reasoning.

## Why a second model at all

ESPC stalls, and not in a way retrying fixes: on 2026-08-31 `tds.hycom.org`
refused every connection for hours, every ESPC tile tier was withdrawn from
the live map, and all five products were briefly held. A different model is
the only answer to a run that goes late. This one is the like-for-like
option — same 1/12 deg, currents and temperature and salinity together.

**It is a PEER, not a fallback** (the owner's call, 2026-09-01). Both models
are fetched every run and the reader chooses; the contract already carries
`source` and `modelRun`, and the map already shows them, so which ocean is on
screen is visible rather than inferred.

## Credentials

Two repository secrets, `COPERNICUSMARINE_SERVICE_USERNAME` and
`COPERNICUSMARINE_SERVICE_PASSWORD`. **There is no API key** — the toolbox
takes a username and password and nothing else, and the username is not the
account email. A Copernicus Marine account is free.
