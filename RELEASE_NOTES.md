`nautilus-trader 2.0.0rc9` rebuilt with the Binance Spot SBE schema `3:6` fix from
upstream commit `ac2e471` (PR #5667, issue #5664).

### What is patched

`crates/adapters/binance/src/spot/http/parse.rs`:

- `decode_exchange_info` accepts `symbols` group block lengths `>= 19`
  (was: exactly 19) and skips the appended bytes before reading nested groups.
- Binance schema `3:6` appends `opoAllowed`, `marketQuoteNotionalLimitAllowed` and
  `limitQuoteNotionalLimitAllowed`, so the block length is now 22.

Without the patch, `BinanceDataClient.connect()` fails with:

```
ValueError: SBE decode error: Invalid block length: expected 19, was 22
```

### Assets

- `nautilus_trader-2.0.0rc9-cp313-cp313-linux_x86_64.whl` — CPython 3.13, Linux x86_64
- `SHA256SUMS`

### Reproduce locally

```bash
git clone --depth 1 --branch v2.0.0rc9 https://github.com/nautechsystems/nautilus_trader.git
git -C nautilus_trader apply /path/to/sbe36.patch
cd nautilus_trader/python
maturin build --release --locked --out ../dist
```

License: LGPL-3.0-only (upstream nautilus_trader).
