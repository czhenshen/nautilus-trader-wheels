# nautilus-trader wheels with Binance Spot SBE 3:6 support

Prebuilt `nautilus-trader==2.0.0rc9` wheels carrying the upstream fix from commit
[`ac2e471`](https://github.com/nautechsystems/nautilus_trader/commit/ac2e47166ebaad3aeff7fa17f3be28fc002a2a9f)
(PR [#5667](https://github.com/nautechsystems/nautilus_trader/pull/5667), issue
[#5664](https://github.com/nautechsystems/nautilus_trader/issues/5664)).

## Why

Binance rolled out Spot SBE schema `3:6` (changelog 2026-11-11) and appended three
`boolEnum` fields to the `symbols` group of `ExchangeInfoResponse`, growing the group
block length from 19 to 22 bytes. `nautilus-trader` up to `2.0.0rc9` (and the current
nightly) rejects any block length other than 19:

```
ValueError: SBE decode error: Invalid block length: expected 19, was 22
```

so `BinanceDataClient` fails to connect (`connection failed`). The patch accepts
block lengths `>= 19` and skips the appended bytes, per the SBE schema-extension
mechanism.

## Install

```bash
curl -LO https://github.com/czhenshen/nautilus-trader-wheels/releases/download/v2.0.0rc9-sbe36/nautilus_trader-2.0.0rc9-cp313-cp313-manylinux_2_34_x86_64.whl
echo "9e7cffe3a7fb08f4fe7b32d8023b4f12d9fc1a0e24a6198859ee4c64133f20e2  nautilus_trader-2.0.0rc9-cp313-cp313-manylinux_2_34_x86_64.whl" | sha256sum -c -
uv pip install ./nautilus_trader-2.0.0rc9-cp313-cp313-manylinux_2_34_x86_64.whl
```

(`SHA256SUMS` next to the wheel was generated before the path prefix was fixed and
lists `../dist/<wheel>`; the hash above is authoritative.)

Requires CPython 3.13 on Linux x86_64 (same as the upstream rc9 wheel).

## Rebuild

Actions -> `build-nautilus-rc9-sbe36` -> *Run workflow*. The job checks out
[`czhenshen/nautilus_trader`](https://github.com/czhenshen/nautilus_trader) at tag
`v2.0.0rc9-cz1` (upstream `v2.0.0rc9` + the fix), builds with maturin and uploads
the wheel to the `v2.0.0rc9-sbe36` release.

## Patch

`patches/sbe36.patch` is exactly the upstream fix (two hunks in
`crates/adapters/binance/src/spot/http/parse.rs`). It is already applied in the
fork [`czhenshen/nautilus_trader`](https://github.com/czhenshen/nautilus_trader)
at tag `v2.0.0rc9-cz1`, which is what the workflow builds.

## License

The wheel is built from [nautilus_trader](https://github.com/nautechsystems/nautilus_trader)
tag `v2.0.0rc9`, licensed under LGPL-3.0-only. Sources: the fork
[`czhenshen/nautilus_trader`](https://github.com/czhenshen/nautilus_trader)
tag `v2.0.0rc9-cz1` (upstream snapshot + `patches/sbe36.patch`).
