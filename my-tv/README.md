# My TV: VU 4K (lushan)

Google TV 12 (SDK 31), firmware STT2.230929.001.102, manufacturer HVCL, at `192.168.1.9` (give it a fixed
address in the router). Set up on 2026-10-25 from `PROMPT-APPLE-TV.md`, using the tested
`examples/vu-4k-lushan` list. Backup of packages, settings and props: `backup/vu-4k-lushan/` (git-ignored).

## What was done

- **Debloat** (`debloat.txt`, all disabled, none uninstalled): Vu ACR telemetry, Google feedback, partner
  auto-installs, store demo mode, Play Games, Google TV app, Odin browser, Family Moment, My Music, Alexa,
  Google Assistant (the remote's mic button no longer works; the phone app has voice), the setup wizard.
  Kept: Chromecast, AirPlay, Miracast, DLNA, OTA updates, USB media player, all com.vt TV services.
- **Launcher**: `glassos/launcher`, now Home (stock `launcherx` disabled by the installer). Dock: Netflix,
  YouTube, Prime Video, Hotstar, Sun NXT; `columns` = 5. Live TV app `com.vt.tv.tvcenter` (hidden tile, in
  the Inputs menu); HDMI/AV come from `com.vt.source.external`.
- **Phone remote**: `http://192.168.1.9:8642` on a phone on the same Wi-Fi; Android app at
  `http://192.168.1.9:8642/app.apk`. Pair with the code the TV shows.
- **Memory**: AirPlay, DLNA and Chromecast built-in disabled too (casting to this TV no longer works).
- **Ad-blocking DNS**: `settings put global private_dns_specifier p2.freedns.controld.com` +
  `private_dns_mode hostname` (27 ms away; NextDNS was 8 ms but blocks nothing without a profile, AdGuard
  47 ms). Ad domains resolve to 127.0.0.1; YouTube, googlevideo and Netflix resolve normally.
  Undo: `settings put global private_dns_mode off`.
- **Phone**: TV Remote app on a Motorola Edge 70 Fusion, paired.
- Launcher and phone app are signed with this Mac's debug key: always update from this Mac.
- Google Assistant came back enabled once after the first debloat; re-disabled. Check with
  `pm list packages -d | grep katniss` if the mic button starts working again.

## Speed (am start -W, ms)

Wake from standby to Home: ~0.6 s (the TV suspends; a real cold boot only happens after unplugging).

| App | Cold before | Cold after (memory + DNS) | Reopen |
|---|---|---|---|
| Netflix | 860 | 784 | 93 |
| YouTube | 827 | 690 | 244 |
| Prime Video | 1202 | 1431 | 90 |
| Hotstar | 2676 | 1925 | 108 |
| Sun NXT | 1489 | 1509 | 213 |
| Apple TV | 986 | 960 | 349 |
| SonyLIV | 1552 | 1345 | 112 |
| ZEE5 | 1899 | 1812 | 105 |

With 1.8 GB RAM about 4-5 apps stay warm; after visiting 8, YouTube, Prime and Hotstar start cold again.

## Undo

    TV_HOST=192.168.1.9 tools/restore.sh my-tv/debloat.txt com.google.android.apps.tv.launcherx

## Trade-offs

USB debugging must stay on for the phone remote to press keys in other apps; while it is on, any app on the
TV may get a shell through adbd on 127.0.0.1. The phone remote is plain HTTP on the home network.
