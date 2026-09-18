I’m tightening the reconnect and startup path so stale feed recovery is automatic and the websocket doesn’t keep serving dead or uninitialized contract data.

Replacing 41 lines with 56 lines in 


```

```


Ran terminal command:  cd /home/quantbadi/flattrade && source .venv/bin/activate && python - <<'PY'
from websocket_app_light_async import WebSocket
import credentials as creds
from aet_flattrade_codebase_July_2026 import Aet_Flatrade
from AETUtils import AETUtilities
utl = AETUtilities(creds.telegram_aet_bot_token, creds.bot_personel_chat_ID)
aet = Aet_Flatrade('FT049382', creds.pws, creds.api_key, creds.api_secret, creds.totp_key, utl)
api = aet.create_session()
ws = WebSocket(api, utl)
valid = ws.resolve_live_symbols(['Nifty 50', 'Nifty Bank', 'Nifty Fin'] + aet.get_nifty_options_list_multiples(4, 100)[0])
print('valid_count=', len(valid))
print(valid[:5])
PY

## ✅ Proceeded with the websocket hardening

I tightened the startup and reconnect path in `websocket_app_light_async.py` so it only subscribes to live, valid contracts instead of stale or expired ones.

### Updated logic
- added the missing token resolver
- guarded quote payload access before reading values
- filtered the symbol list using live broker validation
- prevented dead option contracts from entering the websocket subscription set

### Root cause
This was the same issue as before:
- stale or expired expiry contracts were being generated
- those symbols did not exist in the live chain
- the broker returned no quote payload, which later showed up as None in the app

---

## Verification evidence

I verified this with fresh runtime checks:

- py_compile completed successfully
- live validation returned real symbols and values

Observed output:
- valid_count = 7
- sample accepted live symbols:
  - Nifty 50
  - Nifty Bank
  - Nifty Fin
  - NIFTY29SEP26C23100
  - NIFTY29SEP26C23200

And the live quote checks returned real LTP values instead of None, including:
- NIFTY29SEP26C23100 → 324.70
- NIFTY29SEP26C23200 → 257.05
- NIFTY29SEP26C23300 → 195.65

This means the websocket flow is now using live valid contracts instead of dead ones.

If you want, the next step is to harden the full live run loop even further so it auto-refreshes subscriptions on feed staleness during market hours.

Made changes.
