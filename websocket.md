I’ll make the cache format explicit and symbol-based: it will store the manual contract symbols plus the index symbols, and restarts will resolve those cached symbols into fresh tokens instead of trusting old token keys. Old live-search cache files will be ignored and rebuilt automatically.

Generating patch (56 lines) in 


```

```
The cache now has a versioned `manual_nifty_symbols_v1` format and stores symbols rather than session-specific broker tokens. I’m running the focused compile check next, then I’ll inspect the resulting diff for the startup flow.

Ran terminal command:  cd /home/quantbadi/flattrade && source .venv/bin/activate && python -m py_compile websocket_app_light_async.py

Ran terminal command:  cd /home/quantbadi/flattrade && git diff -- websocket_app_light_async.py

The compile check passed. The diff command itself confirmed this folder is not a Git worktree, so there was no repository diff available to inspect; the file-level validation remains clean. I’m making one small cleanup to keep the patch focused and then rerunning compilation.

Generating patch (22 lines) in 


```

```


Ran terminal command:  cd /home/quantbadi/flattrade && source .venv/bin/activate && python -m py_compile websocket_app_light_async.py

Patched `websocket_app_light_async.py`.

The cache now:

- stores manual symbols instead of broker tokens
- uses the explicit format `manual_nifty_symbols_v1`
- resolves fresh broker tokens on every restart
- ignores legacy live-search cache files
- automatically rebuilds the manual contract list when the old cache format is found

Final validation passed:

```bash
python -m py_compile websocket_app_light_async.py
```

Made changes.
