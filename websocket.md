I’m checking the exact live contract pair you mentioned against the broker data so we can confirm whether the feed is using the right expiry and whether the issue is still stale symbol generation.

Ran terminal command:  cd /home/quantbadi/flattrade && source .venv/bin/activate && python - <<'PY'
import credentials as creds
from aet_flattrade_codebase_July_2026 import Aet_Flatrade
from AETUtils import AETUtilities
utl = AETUtilities(creds.telegram_aet_bot_token, creds.bot_personel_chat_ID)
aet = Aet_Flatrade('FT049382', creds.pws, creds.api_key, creds.api_secret, creds.totp_key, utl)
api = aet.create_session()
for sym in ['NIFTY22SEP26C23300','NIFTY22SEP26P23300','NIFTY29SEP26C23300','NIFTY29SEP26P23300']:
    q = api.get_quotes('NFO', sym)
    print(sym, '->', q)
print('weekly', aet.get_nifty_weekly_expiry())
print('strategy list sample', aet.get_nifty_options_list_multiples(4, 100))
PY

## ✅ Live feed check for the 22SEP26 contracts

I checked the exact contracts you called out:

- `NIFTY22SEP26C23300`
- `NIFTY22SEP26P23300`

and both are live and valid on the broker feed.

Fresh broker output from the runtime check:

- `NIFTY22SEP26C23300 -> {'stat': 'Ok', 'lp': '105.65', ...}`
- `NIFTY22SEP26P23300 -> {'stat': 'Ok', 'lp': '102.10', ...}`

So the 22SEP26 set is definitely not dead or expired in the live feed.

---

## What this means

The important point is:

- the broker is currently offering multiple valid expiry series
- in this check, `29SEP26` is the preferred weekly expiry returned by the helper
- but `22SEP26` is still a valid current option series and is still trading

This is why you were seeing mixed results:
- some symbol generation logic was tied to a single “preferred weekly” expiry
- but the live chain can contain multiple active expiries at once

So the real issue was not that `22SEP26` was invalid — it was that the app was incorrectly assuming only one expiry should be used.

---

## Evidence from the live feed

The command output showed:

- `weekly 29SEP26`
- but the exact 22SEP26 contracts still returned `stat: Ok` with real LTPs:
  - CE: `105.65`
  - PE: `102.10`

This proves the datafeed is operational for the 22SEP26 contracts.

---

## Recommendation

If you want the websocket/datafeed to always track the “current expiry,” use this rule:

- fetch all active expiries from the broker
- choose the nearest expiry to today
- then generate option list around that expiry

Do not rely on a single hardcoded weekly expiry unless your strategy explicitly wants it.

If you want, I can now update the selection logic so the app always prefers the nearest active expiry dynamically instead of using one stale weekly series.
