---
name: login
description: Sign in to Parsec so token savings show up in the user's dashboard. Use when the user wants to connect or link their account, sign in, see whether they are signed in, or sign out.
---

Parsec reports each request's measured savings to the user's dashboard only
once this machine holds their account key. Signing in is a browser handoff —
nothing to paste:

```
${CLAUDE_PLUGIN_ROOT}/bin/parsec login --timeout 110
```

Run that (it fits inside the default tool timeout). It opens the user's
browser at `https://app.getparsec.ai`; they sign in with GitHub and click
**Connect**, and the dashboard hands the key straight back to this machine.
The command prints a masked key and exits 0 when that happens.

While it runs, tell the user: "A browser tab opened — sign in and click
Connect." If it exits non-zero with *timed out*, the tab was not completed;
offer to run it again. If it printed a URL because no browser opened, show
the user that URL so they can open it themselves (it only works from a
browser on this machine).

Confirm with:

```
${CLAUDE_PLUGIN_ROOT}/bin/parsec key show
```

Report back conversationally: it prints a **masked** key and whether shipping
is active. **Never echo a full key** — refer to it only by the masked form.

## Signing out / checking

- `parsec key show` — masked key and where it resolves from (a `PARSEC_API_KEY`
  environment variable overrides the stored file).
- `parsec key clear` — sign out; dashboard reporting stops.

## Machines with no browser

SSH boxes and CI cannot complete the handoff. There, the user mints a key on
the dashboard's account page and it is stored by hand:

```
${CLAUDE_PLUGIN_ROOT}/bin/parsec key set <the-key>
```

Only take this path when the user says there is no browser on this machine.

After signing in, savings from the next request onward appear in the
dashboard (per-model and per-day). The local `/parsec:savings` report is
unaffected — it always reads the on-disk ledger regardless of the key.
