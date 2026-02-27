# codex-account-switcher

Manage multiple OpenAI Codex accounts by swapping authentication tokens.

⚠️ **Sensitive:** This skill reads and writes `~/.codex/auth.json` and `~/.codex/accounts/*.json` (authentication tokens).

## ClawHub

Published on [ClawHub](https://clawhub.com/skills/codex-account-switcher).

## Usage

See [SKILL.md](SKILL.md) for full documentation.

```bash
./codex-accounts.py list          # List saved accounts
./codex-accounts.py add           # Add a new account (interactive)
./codex-accounts.py use <name>    # Switch to an account
./codex-accounts.py auto          # Switch to account with best quota
./codex-accounts.py ensure        # Switch only when active account is below quota thresholds
```

### `ensure` command

`ensure` checks the currently active account and switches only if remaining quota is below thresholds.
Thresholds are **remaining percent**, not used percent.

```bash
# Default thresholds (2% daily remaining, 2% weekly remaining)
./codex-accounts.py ensure

# Custom thresholds
./codex-accounts.py ensure --min-daily-remaining 5 --min-weekly-remaining 10

# JSON output
./codex-accounts.py ensure --json
```

### Recommended systemd user timer

Use a periodic user timer to run `ensure` automatically:

`~/.config/systemd/user/codex-switcher.service`
```ini
[Unit]
Description=Codex account ensure-switcher

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /home/renat/.agents/skills/codex-account-switcher/codex-accounts.py ensure --min-daily-remaining 2 --min-weekly-remaining 2 --max-cache-age-hours 6
```

`~/.config/systemd/user/codex-switcher.timer`
```ini
[Unit]
Description=Run codex ensure-switch periodically

[Timer]
OnBootSec=30s
OnUnitActiveSec=300s

[Install]
WantedBy=timers.target
```

```bash
systemctl --user daemon-reload
systemctl --user enable --now codex-switcher.timer
systemctl --user start codex-switcher.service
journalctl --user -u codex-switcher.service -n 100 --no-pager
```

## Documentation

- [SKILL.md](SKILL.md) — agent-facing reference (commands, behavior, limitations)
- [SETUP.md](SETUP.md) — prerequisites, configuration, and setup instructions
- [ClawHub](https://www.clawhub.com/skills/codex-account-switcher) — install via ClawHub registry

## License

MIT
