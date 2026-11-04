# Package Lists

Rules for the agent:
- Only act on packages that are installed. Check with `adb shell pm list packages <pkg>`.
- `1P` = first-party (phone maker, Google). Method: **disable** (`pm disable-user --user 0`).
- `3P` = third-party. Method: **uninstall** (`pm uninstall --user 0`).
- Skip any package that is a protected app (see `AGENTS.md`). Example: if the phone maker browser is the default browser, skip it.
- Package missing from this file = do not touch.
- If a package fails or breaks a feature, skip it and restore it.

---

## Group ALL-1 — Google optional apps (1P, ALL brands)

| Package | Feature lost |
|---|---|
| `com.google.android.videos` | Google TV (movies) |
| `com.google.android.apps.tachyon` | Google Meet / Duo video calls |
| `com.google.android.apps.magazines` | Google News |
| `com.google.android.apps.podcasts` | Google Podcasts |
| `com.google.android.apps.youtube.music` | YouTube Music |
| `com.google.android.music` | Old Play Music |
| `com.google.android.apps.subscriptions.red` | Google One |
| `com.google.android.apps.wellbeing` | Digital Wellbeing (screen time) |

## Group ALL-2 — Preinstalled third-party apps (3P, ALL brands)

| Package | Feature lost |
|---|---|
| `com.facebook.katana` | Facebook app |
| `com.facebook.orca` | Messenger app |
| `com.facebook.appmanager` | Facebook App Manager (background updater) |
| `com.facebook.services` | Facebook Services (background) |
| `com.facebook.system` | Facebook Installer (background) |
| `com.instagram.android` | Instagram app |
| `com.netflix.mediaclient` | Netflix app |
| `com.netflix.partner.activation` | Netflix background activation |
| `com.linkedin.android` | LinkedIn app |
| `com.spotify.music` | Spotify app |
| `in.amazon.mShop.android.shopping` | Amazon Shopping app |
| `com.amazon.avod.thirdpartyclient` | Prime Video app |
| `com.microsoft.skydrive` | OneDrive app |
| `com.microsoft.office.officehubrow` | Microsoft 365 (Office) app |
| `com.microsoft.office.outlook` | Outlook app |
| `com.skype.raider` | Skype app |
| `com.snapchat.android` | Snapchat app |

Note: the user can install these again from the Play Store if they want them.

---

## Group SAMSUNG — Samsung (1P)

| Package | Feature lost |
|---|---|
| `com.samsung.android.bixby.agent` | Bixby voice assistant |
| `com.samsung.android.bixby.wakeup` | "Hi Bixby" wake word |
| `com.samsung.android.visionintelligence` | Bixby Vision |
| `com.samsung.android.app.spage` | Samsung Free / Daily (left home screen page) |
| `com.samsung.android.game.gamehome` | Game Launcher |
| `com.samsung.android.arzone` | AR Zone |
| `com.samsung.android.app.tips` | Tips app |
| `com.samsung.android.kidsinstaller` | Samsung Kids |
| `com.samsung.android.stickercenter` | Sticker Center |
| `com.sec.android.app.sbrowser` | Samsung Internet (skip if it is the default browser) |

## Group XIAOMI — Xiaomi / Redmi / Poco (1P)

| Package | Feature lost |
|---|---|
| `com.miui.analytics` | Xiaomi analytics (telemetry) |
| `com.miui.msa.global` | Xiaomi system ads (MSA) |
| `com.miui.systemAdSolution` | Xiaomi system ads |
| `com.xiaomi.mipicks` | GetApps store |
| `com.miui.bugreport` | Bug report tool |
| `com.miui.miservice` | Services & feedback |
| `com.xiaomi.glgm` | Xiaomi Games |
| `com.miui.videoplayer` | Mi Video |
| `com.miui.player` | Mi Music |
| `com.mi.globalbrowser` | Mi Browser (skip if it is the default browser) |
| `com.miui.hybrid` | Quick apps |

Note: on some newer HyperOS versions, a package can fail to disable. Skip it.

## Group REALME-OPPO — Realme / Oppo / OnePlus ColorOS (1P)

| Package | Feature lost |
|---|---|
| `com.heytap.browser` | Phone maker browser (skip if it is the default browser) |
| `com.heytap.music` | Phone maker music app |
| `com.heytap.market` | App Market store |
| `com.nearme.gamecenter` | Game Center |
| `com.coloros.video` | Phone maker video app |

## Group VIVO — Vivo / iQOO (1P)

| Package | Feature lost |
|---|---|
| `com.vivo.browser` | Vivo Browser (skip if it is the default browser) |
| `com.vivo.appstore` | V-Appstore |
| `com.vivo.game` | Game Center |
| `com.vivo.space` | vivo Space community |
| `com.vivo.agent` | Jovi voice assistant |

## Group MOTOROLA — Motorola (1P)

| Package | Feature lost |
|---|---|
| `com.motorola.help` | Moto Help app |

Motorola is close to stock Android. Groups ALL-1 and ALL-2 do most of the work.

---

## Other brands or stock Android

Use only groups ALL-1 and ALL-2. You may search the web for more bloat packages for this exact phone model. Add a package only if the source is clear and the feature lost is small. Show it to the user as a new group. Ask first.
