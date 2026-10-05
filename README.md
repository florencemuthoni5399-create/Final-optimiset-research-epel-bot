# SynthEPEL-R75 v2.4.1 — Priced EPEL Measurement

This build keeps the existing authenticated Deriv connection and measurement-only design, but adds **live proposal pricing** at every research signal/horizon.

## What changed
- Requests a real Deriv `proposal` for the signal direction (CALL for RISE, PUT for FALL).
- Uses the actual `ask_price` and `payout` returned by Deriv.
- Records break-even / implied probability = `ask_price / payout`.
- Calculates a **dynamic EPEL threshold** from the quoted gross profit rate:
  `threshold = 1 / (1 + lambda * ((payout - ask_price) / ask_price))`
- Records model probability, 95% Wilson lower bound, and lower-bound price edge versus the live quoted break-even probability.
- Logs the opposite-direction outcome for every resolved test so we can test whether the seed behaves contrarian.
- Dashboard now shows proposal coverage and priced-EPEL information.
- **No buy, sell, or other trade execution is enabled.**

## What is deliberately NOT changed yet
- R_75 remains the research asset.
- The signal is now momentum-aligned: positive lookback movement produces RISE/CALL and negative movement produces FALL/PUT.
- The minimum absolute move filter is now 0.15% by default to reduce low-quality signals. Use 0.20% as a stricter sensitivity test.
- We do not switch to No Touch / Stay Between / volatility-arbitrage contracts yet. Those are separate hypotheses and should be tested independently rather than curve-fit into this run.

## Render environment
Use the same credentials as the working SynthTrade bot. Do not send the API token in chat.

Required:
- DERIV_APP_ID
- DERIV_API_TOKEN
- DERIV_ACCOUNT_TYPE=demo
- ASSET=R_75

Recommended research settings:
- HORIZONS_TICKS=3,5,7,10,15
- LOOKBACK_TICKS=20
- MIN_MOVE_PCT=0.15
- EPEL_ENABLED=true
- EPEL_LAMBDA=1.00
- EPEL_MIN_SAMPLES=50
- WILSON_Z=1.959964
- PROPOSAL_ENABLED=true
- PROPOSAL_TIMEOUT_MS=3000
- ENABLE_TRADING=false

## Output
`data/r75_tick_epel_ledger.csv` is append-only and now includes proposal pricing fields, EPEL threshold, price edge, opposite outcome, and proposal response age.

## v2.4.1 changes
- Fixed the direction-outcome memory growth by replacing per-outcome arrays with bounded `{wins,total}` counters for each horizon/direction.
- Inverted the prior mean-reversion seed to momentum: positive move -> RISE/CALL; negative move -> FALL/PUT.
- Raised the default `MIN_MOVE_PCT` from 0.05% to 0.15%. Set it to 0.20% for a stricter signal-frequency test.
- Trading remains hard-disabled.


### v2.4.1 change
- EPEL lambda default is now **1.00**. This lowers the EPEL qualification threshold relative to the previous 0.50 setting. The live proposal price still determines the actual dynamic threshold for each observation.
- `EPEL_LAMBDA=1.00` is the packaged default; it can still be overridden through the environment.
