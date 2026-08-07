# chioff-ssrf-shared

Shared SSRF-Lite overlay for the **Chicago Offline** community nets.

This repository holds real, on-air RF facts for channels the Chicago Offline
group uses in common: a GMRS simplex/repeater set and a MURS set. It is the
overlay a ChiOff member merges on top of
[`Chicago-Offline/ssrf-lite`](https://github.com/Chicago-Offline/ssrf-lite)
to get the community channels in their codeplug.

## Scope

**In scope:** community-net frequencies, tones, emissions, and channel names
that ChiOff members are expected to share.

**Out of scope:** personal identities, DMR IDs, credentials, precise private
locations, personal repeater pairs, and radio exports. Those belong in a
private per-operator overlay, not here.

## Repository roles

| Repository | Role |
|---|---|
| `ssrf-lite` | Upstream schema + authoritative public library |
| `chioff-ssrf-shared` | **This repo** — real ChiOff community net data |
| `chioff-ssrf-test` | Synthetic `_fixture_` reference overlay (teaching example) |
| `chioff-codeplugger-profiles-shared` | Profiles selecting these channels |
| `chioff-codeplugger-profiles-test` | Synthetic reference profile |

## Channels

### GMRS

| Channel | Freq (MHz) | Tone |
|---|---|---|
| ChiO ALL | 462.575 simplex | DCS 152 |
| ChiO TEAM 1 | 462.600 simplex | DCS 152 |
| ChiO TEAM 2 | 462.625 simplex | DCS 152 |
| ChiO TEAM 3 | 462.650 simplex | DCS 152 |
| ChiO ROAD | 462.675 simplex | CTCSS 141.3 |
| ChiO REPEATER | 462.550 out / 467.550 in | DCS 023 |

`ChiO ALL` is the primary all-hands channel.

Two deliberate exceptions to DCS 152:

- **ChiO REPEATER keeps DCS 023** — this matches the repeater's existing
  on-air configuration. Changing it in data would not change the hardware.
- **ChiO ROAD keeps CTCSS 141.3** — the national GMRS travel/road-assistance
  convention. Putting DCS 152 here would isolate ChiOff radios from
  everyone else using that channel as intended.

### MURS

MURS is listed **twice on purpose**, and the two sets are not interchangeable.

| Channel | Freq (MHz) | Tone |
|---|---|---|
| ChiO MURS 1–3 | 151.820 / 151.880 / 151.940 | DCS 152 |
| ChiO MURS 4–5 | 154.570 / 154.600 | DCS 152 |
| MURS 1–3 | 151.820 / 151.880 / 151.940 | **none** (carrier squelch) |
| MURS 4–5 | 154.570 / 154.600 | **none** (carrier squelch) |

The `ChiO MURS n` channels carry DCS 152 for group traffic. The plain
`MURS n` channels are the published Part 95J bandplan frequencies with **no
tone**, deliberately left open so any MURS radio can monitor and join.

Same frequencies; the tone configuration is the entire difference. That is
why the names stay distinct — and why the untoned set must never have a tone
added to it.

The 154 MHz channels use `20K0F3E` (wide), which Part 95J permits there; the
151 MHz channels are narrow `11K2F3E`.

## Tone choice

`DCS 152` was picked to avoid collision with the common default codes
(`023`, `025`, `026`, `031`) that other Chicagoland GMRS users tend to land
on, and to stay clear of codes already in use in ChiOff members' personal
overlays.

### DCS polarity

Every toned channel is **normal (`N`)** polarity, stated explicitly via
`dcs_tx_polarity` / `dcs_rx_polarity`.

These fields were added upstream in
[`ssrf-lite#8`](https://github.com/Chicago-Offline/ssrf-lite/pull/8),
resolving [`ssrf-lite#7`](https://github.com/Chicago-Offline/ssrf-lite/issues/7),
which this overlay originally had to work around. `N` is the schema default,
so stating it changes nothing functionally — but it puts the intent in the
data instead of leaving it to be assumed.

## Conventions

- All stable IDs are prefixed `chio_` so downstream generators can tell
  shared community records from the authoritative library and from personal
  overlays.
- Channels are **location-less** (portable), so they import regardless of the
  generator's search location.
- Repeater chains are **radio-centric**: `rx` is the repeater output, `tx` is
  the repeater input.

## Validate

With sibling checkouts of `ssrf-lite` and `codeplugger`:

```bash
../codeplugger/.venv/bin/codeplugger-profile \
  ../chioff-codeplugger-profiles-shared/profiles/<radio>/<profile>.yml \
  --ssrf-root ../ssrf-lite/ssrf \
  --ssrf-root ./ssrf \
  --radio-root ../codeplugger/radios
```
