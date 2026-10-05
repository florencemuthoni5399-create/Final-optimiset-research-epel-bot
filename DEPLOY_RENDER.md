# Render deployment — SynthEPEL-R75 v2.3

1. Create/update the Render Node service using `synthtrade-server` as the root directory.
2. Build command: `npm install`
3. Start command: `node bot.js`
4. Set the same `DERIV_APP_ID` and `DERIV_API_TOKEN` used by the working SynthTrade bot.
5. Set `DERIV_ACCOUNT_TYPE=demo`.
6. Keep `ENABLE_TRADING=false`.
7. Set `PROPOSAL_ENABLED=true`.
8. Deploy and open the service URL.

Expected dashboard:
- `Status: authenticated`
- live tick count increasing
- signal count increasing when the 20-tick displacement condition occurs
- proposal requests and quotes increasing
- no trade execution

At each signal the service asks Deriv for a price proposal for the corresponding CALL/PUT and records the returned ask price and payout. The service never sends a `buy` request in this build.
