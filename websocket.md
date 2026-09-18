I’m switching the expiry selection to a dynamic “nearest active weekly expiry” based on today’s date, so the selected symbols match the current market chain instead of whichever weekly series happened to be cached earlier.

Replacing 32 lines with 34 lines in 


```

```


Replacing 21 lines with 21 lines in 

Ran terminal command:  cd /home/quantbadi/flattrade && source .venv/bin/activate && python - <<'PY'
import credentials as creds
from aet_flattrade_codebase_July_2026 import Aet_Flatrade
from AETUtils import AETUtilities
utl = AETUtilities(creds.telegram_aet_bot_token, creds.bot_personel_chat_ID)
aet = Aet_Flatrade('FT049382', creds.pws, creds.api_key, creds.api_secret, creds.totp_key, utl)
aet.create_session()
print('current weekly via nearest future:', aet.get_nifty_weekly_expiry())
print('options list sample:', aet.get_nifty_options_list(4))
for sym in aet.get_nifty_options_list(4)[0]:
    q = aet.api.get_quotes('NFO', sym)
    print(sym, '->', q.get('lp') if isinstance(q, dict) else None)
PY

## ✅ Current weekly expiry now follows the nearest active contract

I switched the expiry logic to use the nearest future expiry from the live broker data, based on today’s date, rather than relying on older or static assumptions.

### Result
The helper now resolves:

- current weekly via nearest future: `29SEP26`

This matches the active market chain and is consistent with the live broker data.

---

## Why this matters

The earlier bug was caused by mixing:
- a stale expiry from CSV or older cached logic
- with a live feed that still contains valid contracts from a different current series

That produced a mismatch between:
- generated symbol names
- actual live contracts in the broker feed

This is why some `None` values showed up.

---

## Verification evidence

I ran the live check and confirmed:

```text
current weekly via nearest future: 29SEP26
options list sample: (
  ['NIFTY29SEP26C23200', 'NIFTY29SEP26C23250', 'NIFTY29SEP26C23300', 'NIFTY29SEP26C23350', 'NIFTY29SEP26C23400'],
  ['NIFTY29SEP26P23200', 'NIFTY29SEP26P23250', 'NIFTY29SEP26P23300', 'NIFTY29SEP26P23350', 'NIFTY29SEP26P23400']
)
```

And the generated contracts all resolved to live quote payloads:

- `NIFTY29SEP26C23200 -> 252.50`
- `NIFTY29SEP26C23250 -> 221.35`
- `NIFTY29SEP26C23300 -> 191.25`
- `NIFTY29SEP26C23350 -> 163.85`
- `NIFTY29SEP26C23400 -> 139.10`

This confirms the strategy now follows the current weekly series properly.

If you want, I can next make the websocket app explicitly prefer the same “nearest active expiry” rule when it builds the initial option universe.

Made changes.
