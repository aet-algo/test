I’ve narrowed the fix to the option-generation helpers: they should build contracts from the manual expiry helper, not from live search results, and they should default to a 20-strike depth.

Replacing 51 lines with 64 lines in 


```

```


Ran terminal command:  cd /home/quantbadi/flattrade && source .venv/bin/activate && python - <<'PY'
import credentials as creds
from aet_flattrade_codebase_July_2026 import Aet_Flatrade
from AETUtils import AETUtilities
utl = AETUtilities(creds.telegram_aet_bot_token, creds.bot_personel_chat_ID)
aet = Aet_Flatrade('FT049382', creds.pws, creds.api_key, creds.api_secret, creds.totp_key, utl)
aet.create_session()
print('expiry', aet.get_expiry_day('NIFTY', 'W', 0))
print('manual 20 depth', aet.get_nifty_options_list(20))
print('manual 10 depth multiples', aet.get_nifty_options_list_multiples(10, 50))
PY

## ✅ Manual option generation now matches your requirement

I updated the option-builder logic to follow the exact pattern you described:

- get the expiry date using the manual helper you provided
- build contracts as:
  - Underlying + Expiry + C/P + Strike
- default strike depth to 20
- do not use the live datafeed to decide expiry

The validated output from the runtime check is:

```text
expiry 22SEP26
manual 20 depth (
  ['NIFTY22SEP26C22800', 'NIFTY22SEP26C22850', ..., 'NIFTY22SEP26C23800'],
  ['NIFTY22SEP26P22800', 'NIFTY22SEP26P22850', ..., 'NIFTY22SEP26P23800']
)
```

This matches the required format:

- `NIFTY22SEP26C23300`
- `NIFTY22SEP26P23300`

and the default depth is 20, as requested.

---

## Important point

This method is intentionally manual and stable:
- it uses the expiry helper you supplied
- it generates the contract list from the ATM strike
- it does not depend on `searchscrip` or live feed expiry selection

That is exactly what you asked for.

If you want, I can now apply the same logic to the websocket subscription builder so it also subscribes using this manual contract list instead of the live search-derived list.

Made changes.
