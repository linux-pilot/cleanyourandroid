# cleanyourandroid — Restore

You are an agent. The user wants to undo changes. Follow `AGENTS.md` hard rules. Ask before every change.

## Steps

1. **Connect ADB again.** Step 8 turned it off. Do Step 0 in `AGENTS.md` again.
2. **Read `LOG.md`.** If it is missing: ask the user which apps to restore. Use `PACKAGES.md` as a guide.
3. **Ask scope:** restore everything, or one group? Ask again per group. Show the full package list.
4. **Restore each package by its `Undo` value:**

| Undo | Command |
|---|---|
| `enable` | `adb shell pm enable --user 0 <pkg>` |
| `install-existing` | `adb shell cmd package install-existing --user 0 <pkg>` |
| `play-store` | ADB cannot do this. Give the user the link: `https://play.google.com/store/apps/details?id=<pkg>`. App data is gone. |

5. **Private DNS** (if changed): use the old values in `LOG.md` > Before.
   - Old mode value: `adb shell settings put global private_dns_mode <old>`. If it was `null`, use `opportunistic`.
   - Old specifier value: if `null`, run `adb shell settings delete global private_dns_specifier`. Else put the old value.
6. **Animations** (if changed): put the old values. If `null`, use `1.0`.
   ```
   adb shell settings put global window_animation_scale <old>
   adb shell settings put global transition_animation_scale <old>
   adb shell settings put global animator_duration_scale <old>
   ```
7. **Cleanup cannot be undone.** Deleted temp files, logs, and APK files do not return. Caches and thumbnails rebuild by themselves. Tell the user.
8. Ask "Reboot now?". Then `adb reboot`.
9. **Check:** `adb shell pm list packages -d`. Restored apps must not be in this list.
10. Add a `## Restore` section to `LOG.md`: what was restored, what failed.
11. Ask "Turn OFF ADB and Developer options?". Do Step 8 of `AGENTS.md`.

## If the phone is stuck or an app crashes

1. Restart the phone: hold the power button 10 seconds.
2. If it still does not start: start in Safe Mode (hold the power button, long-press **Power off**, tap **OK**). Then connect ADB and run these steps.
3. Last option: Settings > System > Reset options. This erases all data. Warn the user first.
