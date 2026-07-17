# SPX_GO — SPX Options Signal Copier

SPX_GO is a single-binary Windows bot that receives SPX / SPXW options signals over a
WebSocket relay and places the matching trades on **your own Charles Schwab account**.
One `spxgo.exe` — no Python, no runtime to install.

## Download

Grab the latest release zip from this repo (e.g. `SPX_GO-2026.07.16.zip`) and unzip it to
a folder of your choice, e.g. `C:\SPX_GO`.

**What's in the zip**

| File | Purpose |
|---|---|
| `spxgo.exe` | The bot (self-contained Windows binary) |
| `.env.example` | Config template — copy to `.env` and edit |
| `config.json.example` | Alternative JSON config template (env vars override it) |
| `schwab_tokens.json.example` | Schwab API credential template |
| `install_token_task.ps1` | One-click install of the weekly Schwab token-refresh task |
| `SPX_GO_weekly_token.xml` | The same scheduled task as an importable Task Scheduler XML |
| `README.md`, `USER_GUIDE.md` | Documentation (USER_GUIDE has the full step-by-step setup) |

## Quick start

1. **Unzip** to `C:\SPX_GO` and copy `.env.example` → `.env`.
2. **License**: put your license token in `.env` as `SPXGO_LICENSE_TOKEN=...`.
3. **Schwab API**: create an app at [developer.schwab.com](https://developer.schwab.com),
   then copy `schwab_tokens.json.example` → `schwab_tokens.json` and fill in your
   **App Key** and **Secret** (keep the file next to `spxgo.exe`).
4. **Authorize** (interactive, opens a browser):
   ```powershell
   .\spxgo.exe -token
   ```
   Schwab login tokens last **7 days** — run `install_token_task.ps1` once to schedule the
   refresh every Sunday morning, or re-run `-token` manually each week.
5. **Run**:
   ```powershell
   .\spxgo.exe
   ```
   It validates the license, connects to the relay, and logs incoming SPX signals with a
   per-minute P/L report marked from live Schwab option quotes.

## Demo vs. real money

The bot ships in **demo mode: signals and P/L are logged, no orders are placed.**

Real trading must be armed explicitly with **all three** settings:

```ini
SPXGO_MODE=real
SPXGO_LIVE_TRADE=on
SPXGO_REAL_CONFIRM=on
```

Leave any of them off and nothing reaches your account. `SPXGO_MAX_CONTRACTS` (default 5)
is a hard per-order cap that is always enforced.

## Built-in safety

- **End-of-day flatten**: positions are closed at 15:45 ET unless every short strike is
  safely out of the money; hard cutoff at 15:58 ET.
- **License checks**: validated at startup, then re-checked every 30 days; a revoked
  token stops trading.
- **Per-order contract cap**: `SPXGO_MAX_CONTRACTS` is enforced regardless of signal size.

## Requirements

- Windows 10/11
- A Charles Schwab brokerage account with API access (free at developer.schwab.com)
- A license token for the signal relay

## Support

See `USER_GUIDE.md` inside the zip for the full walkthrough, including the Schwab app
setup screenshots-level detail and troubleshooting.
