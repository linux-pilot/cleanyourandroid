# cleanyourandroid

Make an old Android phone faster. An AI agent does the work. You say yes or no at each step.

## What it does

- **Debloat.** System apps are disabled. Third-party apps are uninstalled. No root.
- **Private DNS.** Sets `dns.adguard-dns.com` (Android 9+). It blocks ads and trackers in some apps. It does **not** block YouTube ads.
- **Cleanup.** App cache, temp files, logs, thumbnails, empty folders. You choose which APK files to delete. Optional deep scan (lists only).
- **Faster animations.** Set to 0.5x.
- **Report.** `LOG.md` shows before and after.
- **Lock down.** Last step turns OFF ADB and Developer options.

## What you need

- Claude Code, Antigravity, or any agent that reads `AGENTS.md`.
- An Android phone. DNS needs Android 9+.
- Option A (RECOMMENDED): a PC and a USB cable.
- Option B: phone only, with Termux. Needs Android 11+.

## Start

1. Download this folder.
2. Open the folder in your agent.
3. Type: `Read AGENTS.md and start.`

The agent asks you for `[Brand] [Model] [Android Version]`, then guides you.

## Undo

Type: `Read RESTORE.md and start.`

## Files

| File | Use |
|---|---|
| `AGENTS.md` | Rules and steps for the agent |
| `CLAUDE.md` | Points Claude Code to `AGENTS.md` |
| `PACKAGES.md` | Bloat lists by brand |
| `RESTORE.md` | Undo steps for the agent |
| `LOG.md` | Made at run time. Shows what the agent did. Not in git. |

## Safety

- The agent asks before every change. It lists the full package names for each group.
- It never touches dialer, SMS, launcher, keyboard, camera, Google Play Services, System UI, or bank/UPI apps.
- It never deletes photos or personal files without a per-file "yes".
- Deleted cache and temp files cannot come back. Disabled apps can. User apps need the Play Store.
- Use at your own risk.

## License

MIT. See `LICENSE`.
