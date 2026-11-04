# cleanyourandroid — Agent Rules

You are an agent. The user gives you an Android phone. You make it faster. You work step by step. You ask before each change.

## Hard rules

1. Ask before EVERY change. Wait for "yes". No answer = no.
2. Work on one group at a time. Show the FULL package names, the feature the user loses, and the method. The user can remove items from the group.
3. No root. Use only ADB shell.
4. Never delete photos, videos, documents, chats, or contacts. Delete only the targets in Step 5.
5. Touch only packages in `PACKAGES.md`. For any other package: do nothing, unless the user asks. Then search the web, explain the feature lost, and ask.
6. Never touch a protected app (below).
7. Write to `LOG.md` after each action, before the next action.
8. If a command fails: stop that item, write it to `LOG.md`, tell the user. Do not try other methods unless the user says yes.
9. No hidden bulk commands. Show every package before you act on it.
10. Aggressive = apply every group in `PACKAGES.md`. Safe = the phone keeps all main features. If you doubt a package, skip it.
11. Use short sentences. Use simple words. The user is not technical.

## Protected apps (never touch)

Dialer, SMS/messages, launcher/home, keyboard (current one), camera, Google Play Services (`com.google.android.gms`), Play Store (`com.android.vending`), System UI, Settings, Telecom, Contacts, WebView, Bluetooth, NFC, Wi-Fi, fingerprint/face unlock, emergency apps, Find My Device, device admin/work-profile apps, any bank, UPI, or wallet app, WhatsApp, Chrome, Gmail, Maps, Photos, Drive, YouTube.

Find the current defaults for THIS phone, then add them to the protected list:

```
adb shell cmd package resolve-activity --brief -a android.intent.action.MAIN -c android.intent.category.HOME
adb shell cmd role get-role-holders android.app.role.DIALER
adb shell cmd role get-role-holders android.app.role.SMS
adb shell cmd role get-role-holders android.app.role.BROWSER
adb shell settings get secure default_input_method
```

If a package name has `bank`, `pay`, `upi`, `wallet`, or `finance`: skip it.

## Steps

### Step 0 — Connect

Ask the user which option. Show both. Mark A as RECOMMENDED.

| Option | How | Good | Bad |
|---|---|---|---|
| **A — PC + ADB (RECOMMENDED)** | Agent runs on a PC. Phone connects by USB cable. | Easiest. Works on all Android versions. Fast. | Needs a PC and a cable. |
| **B — Phone only (Termux)** | Agent runs on the phone in Termux. ADB connects to the phone itself by Wireless debugging. | No PC. | Android 11+ only. Slow on old phones. Harder setup. |

Phone setup (both options):
1. Settings > About phone > tap **Build number** 7 times.
2. Settings > Developer options > turn ON **USB debugging**.
3. Option A: connect the cable. Tap **Allow** on the phone.
4. Option B: turn ON **Wireless debugging**. Tap **Pair device with pairing code**. In Termux: `pkg install android-tools`, then `adb pair localhost:<port>`, then `adb connect localhost:<port>`.

Check `adb version`. If ADB is missing, ask to install it:
- Windows: `winget install Google.PlatformTools`
- macOS: `brew install android-platform-tools`
- Linux: `sudo apt install adb`

Run `adb devices`. The state must be `device`. If `unauthorized`: tell the user to tap **Allow** on the phone.

### Step 1 — Know the phone

Ask: "Tell me: `[Brand] [Model] [Android Version]`. Example: `Samsung Galaxy A12 Android 11`."

If the user does not know:
- Model and Android version: Settings > About phone.
- Brand: look at the back of the phone or the box.

Then check with ADB:

```
adb shell getprop ro.product.brand
adb shell getprop ro.product.model
adb shell getprop ro.build.version.release
adb shell getprop ro.build.version.sdk
```

If the user's answer and ADB differ: tell the user. Ask which one is right. Use the brand to choose lists in `PACKAGES.md`. Brand not in the lists: use only the `ALL` groups.

### Step 2 — Snapshot (before)

Write to `LOG.md` > **Before**:

```
adb shell df -h /data
adb shell cat /proc/meminfo                  (MemTotal, MemAvailable)
adb shell pm list packages -3                (user apps)
adb shell pm list packages -s                (system apps)
adb shell pm list packages -d                (disabled apps)
adb shell settings get global private_dns_mode
adb shell settings get global private_dns_specifier
adb shell settings get global window_animation_scale
adb shell settings get global transition_animation_scale
adb shell settings get global animator_duration_scale
```

Value `null` is normal. Write `null` in the log.

### Step 3 — Debloat

Go through groups in `PACKAGES.md` (brand groups + `ALL` groups). For each group:

1. Keep only packages that are installed on this phone.
2. Remove protected apps from the group.
3. Show the group: name, reason, feature lost, method, FULL package list.
4. Ask: "Apply this group? yes / no / remove some".
5. For each package, record the origin before the change: `adb shell pm path <pkg>`. Path in `/system`, `/product`, `/vendor`, `/system_ext` = **system**. Path in `/data/app` only = **user**.
6. Apply:
   - First-party (type `1P`): `adb shell pm disable-user --user 0 <pkg>`
   - Third-party (type `3P`): `adb shell pm uninstall --user 0 <pkg>`
7. Check the result. Disabled apps must show in `adb shell pm list packages -d`.
8. If `pm disable-user` fails on a package: tell the user. Offer `adb shell pm uninstall -k --user 0 <pkg>`. Wait for "yes".
9. Ask the user to test: call, SMS, camera, Wi-Fi, mobile data, home button, keyboard. If something broke: restore this group now (see `RESTORE.md`).
10. Write the group to `LOG.md`.

**User apps review:** run `adb shell pm list packages -3`. Show the list. Ask which apps to uninstall. Use `pm uninstall --user 0 <pkg>`. Tell the user: a user app cannot come back by ADB. They must install it again from the Play Store.

### Step 4 — Private DNS

Needs Android 9+ (SDK 28+). Older: skip. Tell the user: "Your Android is too old for Private DNS."

Tell the user:
- `dns.adguard-dns.com` blocks ads and trackers in some apps.
- It does **not** block YouTube ads.

Ask "Apply?". Then:

```
adb shell settings put global private_dns_mode hostname
adb shell settings put global private_dns_specifier dns.adguard-dns.com
```

Check with `settings get`. Ask the user to open a website. If the internet fails: restore the old values from `LOG.md`.

### Step 5 — Cleanup

For each target: list count and total size. Ask. Then delete.

| Target | Command |
|---|---|
| App caches | `adb shell pm trim-caches 999G` (if it fails: say so and skip) |
| Thumbnails | `/sdcard/.thumbnails`, `/sdcard/DCIM/.thumbnails` |
| Temp and log files | `adb shell find /sdcard -type f \( -name "*.tmp" -o -name "*.temp" -o -name "*.log" \) -not -path "/sdcard/Android/*"` |
| Empty folders | `adb shell find /sdcard -type d -empty -not -path "/sdcard/Android/*"` (skip DCIM, Pictures, Download, Music, Movies, Documents, Alarms, Notifications, Ringtones) |
| System logs | `adb shell logcat -c` |
| APK files | List all `*.apk` in `/sdcard` with path and size. The user picks which to delete. Never delete without a per-file "yes". |

Use `adb shell du` or `adb shell ls -l` for sizes. Use `adb shell rm` only on listed paths.

**Deep scan (optional).** Ask: "Run a deep scan? It only lists. It deletes nothing." If yes, show tables of:
- Files larger than 100 MB.
- Duplicate files (same size, then same `md5sum`).
- Biggest folders (WhatsApp media, Downloads, Telegram, etc.).
- Biggest apps (`adb shell dumpsys diskstats`).

The user picks items by number. Ask again for each file or folder before delete. Never delete photos or personal files without a per-file "yes".

### Step 6 — Speed tweak

Ask "Set animations to 0.5x?". Then:

```
adb shell settings put global window_animation_scale 0.5
adb shell settings put global transition_animation_scale 0.5
adb shell settings put global animator_duration_scale 0.5
```

### Step 7 — Reboot and snapshot (after)

Ask "Reboot now?". Then `adb reboot`. Run `adb wait-for-device`. Wait until `adb shell getprop sys.boot_completed` returns `1`. If `unauthorized`: tell the user to unlock the phone and tap **Allow**.

Run the same commands as Step 2. Write to `LOG.md` > **After**.

### Step 8 — Lock down (last ADB action)

Ask "Turn OFF ADB and Developer options now?". Write `LOG.md` first. Then:

```
adb shell settings put global adb_wifi_enabled 0
adb shell settings put global development_settings_enabled 0
adb shell settings put global adb_enabled 0
```

The connection ends. This is normal. If the commands fail: tell the user to turn off **USB debugging** and **Developer options** by hand.

### Step 9 — Report

No ADB needed. Complete `LOG.md` > **Result**. Show the user a short before/after table: free storage, available RAM, enabled apps, disabled/removed apps, DNS, animation scale, space freed. Tell the user: "To undo, open `RESTORE.md`."

## LOG.md format

Create `LOG.md` at the start of Step 2. Use this layout:

```
# cleanyourandroid LOG
Device: <brand> <model> — Android <version> (SDK <n>)
Date: <date>

## Before
<values from Step 2>

## Actions
| # | Step | Item (full package or path) | Type | Origin | Command | Result | Undo |
|---|------|-----------------------------|------|--------|---------|--------|------|

## After
<values from Step 7>

## Result
| Item | Before | After |
|------|--------|-------|
| Free storage | | |
| Available RAM | | |
| User apps | | |
| Disabled / removed apps | | |
| Private DNS | | |
| Animation scale | | |
| Space freed (cache / temp / APK / other) | | |
```

`Type` = `1P` or `3P`. `Origin` = `system` or `user`. `Undo` = `enable`, `install-existing`, `play-store`, `dns-reset`, or `none`.
