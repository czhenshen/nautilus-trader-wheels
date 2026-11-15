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
curl -LO https://github.com/czhenshen/nautilus-trader-wheels/releases/download/v2.0.0rc9-sbe36/nautilus_trader-2.0.0rc9-cp313-cp313-linux_x86_64.whl
curl -LO https://github.com/czhenshen/nautilus-trader-wheels/releases/download/v2.0.0rc9-sbe36/SHA256SUMS
sha256sum -c SHA256SUMS
uv pip install ./nautilus_trader-2.0.0rc9-cp313-cp313-linux_x86_64.whl
```

Requires CPython 3.13 on Linux x86_64 (same as the upstream rc9 wheel).

## Rebuild

Actions -> `build-nautilus-rc9-sbe36` -> *Run workflow*. The job checks out the
upstream tag, applies `patches/sbe36.patch`, builds with maturin and uploads the
wheel to the `v2.0.0rc9-sbe36` release.

## Patch

`patches/sbe36.patch` is exactly the upstream fix (two hunks in
`crates/adapters/binance/src/spot/http/parse.rs`).

## License

The wheel is built from [nautilus_trader](https://github.com/nautechsystems/nautilus_trader)
tag `v2.0.0rc9`, licensed under LGPL-3.0-only. Sources: the upstream tag plus
`patches/sbe36.patch` in this repository.
