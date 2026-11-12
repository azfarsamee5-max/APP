# Salah Lock

Android app (Kotlin, Jetpack Compose, Room, WorkManager, Adhan) that locks other apps
until the day's prayers, Quran reading and Hadith reading are done.

## Build
1. Open this folder in **Android Studio Hedgehog (2023.1) or newer** (JDK 17). Let Gradle sync.
2. Plug in a real phone (Android 8+), enable USB debugging, press Run.
   Overlay and usage-access behaviour is unreliable on emulators.

## Grant permissions (onboarding screen walks you through it)
| Permission | Where | Why |
|---|---|---|
| Display over other apps | Settings > Apps > Special access | draws the lock screen |
| Usage access | Settings > Apps > Special access | detects the foreground app |
| Notifications | prompt | prayer reminders |
| Alarms & reminders | Settings > Special access | exact prayer-time alarms |
| Battery: unrestricted / ignore optimization | prompt | stops the OS killing the guard |
| Device admin (optional) | Settings tab | uninstall needs deactivation first |

On Xiaomi/Oppo/Vivo/Samsung also enable "Autostart" / "Allow background activity" for the app.

## Files
- `Core.kt` - prefs, prayer windows (Adhan), permission helpers
- `Db.kt` - Room entities/DAO (prayer logs, daily Quran/Hadith progress, emergency log)
- `Blocking.kt` - `BlockPolicy` (when to lock) and `BlockerService` (usage polling + overlay)
- `Alarms.kt` - exact alarms, boot receiver, WorkManager, device admin
- `MainActivity.kt` - ViewModel + navigation, `Screens.kt` - all UI screens

## Placeholder data (you must replace)
`app/src/main/assets/quran.json` and `hadith.json` contain ONE placeholder entry each.
Scripture is deliberately not typed from memory. Generate the files from verified sources:
- Quran: Tanzil.net (Uthmani text + English `en.sahih` + Urdu `ur.jalandhry`), merged into
  `[{"surah":1,"ayah":1,"ar":"...","en":"...","ur":"..."}, ...]`
- Hadith: a verified Sahih Bukhari dataset, as `[{"id":"bukhari-1","ref":"Bukhari 1","ar":"...","en":"...","ur":"..."}]`

For a real Adhan sound put `adhan.mp3` in `res/raw` and set it on the "adhan" channel (see `Alarms.kt`).

## Known limits (be honest with yourself)
- Settings is in the always-allowed list so you can grant permissions; that also means you can
  revoke Usage Access there. The persistent warning and device admin slow that down, not stop it.
  For stricter use remove `com.android.settings` from `defaultAllowed()` after setup.
- AccessibilityService detection mode is not implemented (UsageStats only).
- Launcher icon is a system placeholder; add your own in `res/mipmap-*`.
- Google Play restricts overlay/usage-stats apps; sideloading is simplest for personal use.
