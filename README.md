# 6-cyl-generator — ACER Multi-Cylinder Prime Atlas

Renders an office PID roster as **six spatially-distinct prime cylinders** — a population/geometry
atlas that resists collapsing the system into a flat graph. Each PID is placed by a **verified,
deterministic** Brown-Hilbert coordinate transform, then folded into one of six cylinders by
`bh_index mod 6`.

This repo is the **generator only**. It reads its data (the office feed) and its coordinate engine
at runtime — neither the feed nor any rendered output is committed here (see `.gitignore`).

## The transform (real fields only)

```
reg            = mintPid(name) -> sector / lane / glyph
bh_index       = sector*3072 + lane*1024 + glyph
cylinder_phase = bh_index mod 6              -> WHICH of 6 cylinders
cylinder_ring  = floor(bh_index / 6)         -> height up that cylinder
prime_band     = PRIME_CUBE_PRIMES[sector % 11]   (13..131, cubed = anchor)
watcher_lane   = [hookwall, gnn, shannon][lane % 3]
```

## Usage

```bash
# positional, or via env
node build-multi-cylinder-atlas.mjs <office-feed.hbp> <out.html>

ASOLARIA_OFFICE_FEED=/path/to/office-feed.hbp \
ASOLARIA_NN_EXPORTER=/path/to/pre-existence-graph-exporter.mjs \
  node build-multi-cylinder-atlas.mjs

node build-multi-cylinder-atlas.mjs --self-test   # proves the engine resolves; invents no data
```

### Inputs (provided at runtime, not in this repo)
- **Office feed** — newline-delimited `REG|...` rows (one per PID). Point at it with
  `ASOLARIA_OFFICE_FEED` or the first positional arg.
- **Coordinate engine** — `pre-existence-graph-exporter.mjs` (the NN-repo engine exporting
  `preExistenceNode`, `PRIME_CUBE_PRIMES`, `PRIME_CUBES`, `WATCHER_LANES`). Provide via
  `ASOLARIA_NN_EXPORTER`, place beside this file, or keep the sibling-repo layout.

### Output
A single self-contained, rotatable 3D HTML viewer (no external deps, offline-safe canvas 3D).

## Honest boundaries (also rendered in the page header)
- Descriptor coordinates are **REAL** (deterministic from the feed).
- The mod-6 fold is **not** a true Hilbert `d2xyz` curve — it is a linear-index fold (marker).
- Any GNN watch overlay is **LIVE but proposal-not-proof**.
- Live nanosecond process telemetry is **NOT** claimed — these are descriptor positions.

## Last verified build
`plotted=726 · phases {0:106,1:124,2:120,3:121,4:133,5:122} · watchers {shannon:250,hookwall:247,gnn:229} · lanes {real:470,logical:231,frozen:25}` — built 2026-06-26 on the Linux/WSL lane (node v20).

> Distinct from `asolaria-multi-cylinder-v2` (the 15-cylinder / 16-level / 81,434-surface population
> atlas), which is a substrate-owned snapshot with its own generator — not this tool.
