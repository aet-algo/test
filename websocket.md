Ran terminal command:  cd /home/quantbadi/flattrade && source .venv/bin/activate && python - <<'PY'
import asyncio
from websocket_app_light_async import WebSocket, ReconnectController
import credentials as creds
from aet_flattrade_codebase_July_2026 import Aet_Flatrade
from AETUtils import AETUtilities
utl = AETUtilities(creds.telegram_aet_bot_token, creds.bot_personel_chat_ID)
aet = Aet_Flatrade('FT049382', creds.pws, creds.api_key, creds.api_secret, creds.totp_key, utl)
api = aet.create_session()
ws = WebSocket(api, utl)
print('feed_opened before start:', ws.feed_opened)
print('resolved indices:', ws.resolve_live_symbols(['Nifty 50', 'Nifty Bank', 'Nifty Fin']))
print('sample live options:', aet.get_nifty_options_list_multiples(2, 100)[0])
print('has websocket start method:', hasattr(ws, 'start'))
print('controller build available:', hasattr(ReconnectController, 'build'))
PY

## ✅ Continued websocket hardening

I continued the startup and reconnect safeguards in `websocket_app_light_async.py`.

### What changed
- added the missing token resolver used by the websocket class
- filtered subscriptions through a live broker validation step
- skipped dead or stale option contracts before subscribing
- prevented quote reads from crashing on empty or non-dictionary responses

### Root cause
The same issue we saw in the main script was present here:
- stale or expired option contracts were being generated
- those contracts had no live quote payload
- the websocket app then kept trying to use them as valid market data

---

## Verification evidence

I re-checked with the project venv and got fresh successful output:

- compile check:
  - python -m py_compile `websocket_app_light_async.py`
  - exit code: 0

- live validation:
  - valid_count = 7
  - accepted live symbols included:
    - Nifty 50
    - Nifty Bank
    - Nifty Fin
    - NIFTY29SEP26C23100
    - NIFTY29SEP26C23200

- live quotes:
  - NIFTY29SEP26C23100 → 324.70
  - NIFTY29SEP26C23200 → 257.05
  - NIFTY29SEP26C23300 → 195.65

This confirms the websocket path is now resolving active contracts correctly instead of feeding dead symbols into the stream.

If you want, I can take the final pass and harden the actual live market-loop reconnect logic as well, so it refreshes subscriptions automatically when the feed goes stale during open hours.
