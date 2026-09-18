Compacted conversationI’m updating the websocket subscription path so it uses the same manual NIFTY contract list generation instead of the live search-derived symbols, then I’ll validate the file compiles cleanly.

Read , lines 500 to 640

Searched for regex `def get_nifty_options_list|def get_nifty_options_list_multiples|def get_expiry_day` (`**/aet_flattrade_codebase_July_2026.py`), 4 results

Read , lines 1180 to 1605

The fix is straightforward: the websocket startup was still building a candidate list from a live broker option search before subscribing. I’m switching that path to the same static/manual NIFTY expiry builder so the feed subscribes on the deterministic manual contract set.

Generating patch (51 lines) in 


```

```


Ran terminal command:  cd /home/quantbadi/flattrade && source .venv/bin/activate && python -m py_compile websocket_app_light_async.py

## ✅ Websocket subscription now uses the manual NIFTY contract list

I changed the subscription path in `websocket_app_light_async.py` so it no longer builds the candidate list from the live broker search-derived option set.

It now does this:

- builds contracts via `controller.aet.get_nifty_options_list(42)`
- combines call/put arrays into a deterministic manual list
- appends the index symbols
- filters that list with `resolve_live_symbols(...)`
- subscribes only to the valid resolved symbols

This keeps the websocket feed aligned with the same manual expiry/strike logic you requested, instead of the earlier live-search-based approach.

### Verification
I compiled the file successfully with:

```bash
cd /home/quantbadi/flattrade && source .venv/bin/activate && python -m py_compile websocket_app_light_async.py
```

This completed without errors, which confirms the updated file is syntactically valid.

If you want, I can also patch the cache-loading behavior so future websocket restarts reuse the same manual symbol set without depending on the old live-search cache format.

Made changes.
