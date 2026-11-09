# Bedroom TV: JMGO N1S 4K (S91A, lonavla)

Google TV on Android 11 (SDK 30), firmware `RTMA.250416.195`. Cleaned on 9 November 2026.

Every command below needs debugging back on first: Settings → System → Developer options → **USB debugging**
on (that opens port 5555 on this firmware), then press **Allow** on the projector. Replace `<ip>` with the
projector's address (Settings → Network & Internet → your network).

## What changed

| | |
|---|---|
| Turned off (13) | JMGO tracker, help centre, retail demo, Google feedback, Play Games, YouTube Music, partner rows, JMGO player, Google Assistant (2 packages), Google data-saver VPN, setup wizard, Google TV launcher |
| Theme | `theme-ambient-glass/`: the repo's ambient-glass with your dock and 4 columns. Undo: `TV_HOST=<ip> tools/theme.sh --reset` |
| Kept | YouTube, Netflix, Prime Video, Chromecast, Google screensaver, and everything for autofocus, keystone, the gimbal stand and the remote |
| Installed | The cleanyourtv launcher (`com.gathin.tvhome`) as Home. Dock: Nuvio, Netflix, YouTube, Prime Video |
| Gear button | Opens a small Settings panel over any app (`settings_key_scancodes` 256, `HomeKeyService`). The trade: held Volume Up moves one step per press |
| Wi-Fi | The hotspot was forgotten and re-joined so Android owns it, not the setup wizard |

Nothing was uninstalled. The list is [`debloat.txt`](debloat.txt); the backup taken before any change is in
`../../backup/`, and `tools/disable.sh` kept its own record in `~/.cleanyourtv/`.

## Put everything back

Before returning the projector, a factory reset is enough: Settings → System → About → Reset. It turns every
package back on, removes the launcher and signs out your Google account.

To undo only this session, from the repo folder:

```bash
TV_HOST=<ip> tools/restore.sh work/bedroom-projector/debloat.txt com.google.android.apps.tv.launcherx
TV_HOST=<ip> tools/venv/bin/python tools/tv.py 'pm uninstall com.gathin.tvhome'
```

The restore puts Home back on the Google TV launcher before the second line removes this one.

## If this folder is lost

Everything needed is on the `bedroom-projector` branch of `ansharora1810/cleanyourtv`: the list, this folder, the
backup in `backup/` and the screenshots in `shots/`. The ADB key is not, so the projector asks to Allow again.

```bash
gclp ansharora1810/cleanyourtv ~/Documents/personal/cleanyourtv -b bedroom-projector
cd ~/Documents/personal/cleanyourtv
python3 -m venv tools/venv && tools/venv/bin/pip install -r tools/requirements.txt
mkdir -p ~/.cleanyourtv && cp work/bedroom-projector/cleanyourtv-record/*.json ~/.cleanyourtv/
```

The record is filed under the address it was taken at (10.223.93.44). If the projector has another address now,
`restore.sh` says it has no record and turns on the whole list instead, which gives the same result here.

## Turn one app back on

```bash
TV_HOST=<ip> tools/venv/bin/python tools/tv.py 'pm enable com.google.android.katniss'
```

Assistant also needs `com.google.android.tv.assistant`. Comment the line out in `debloat.txt` so a later run
does not turn it off again.

## Change the dock or the look

- Dock order and apps: `dock_apps` in `launcher/app/src/main/res/values/config.xml`, then reinstall:

  ```bash
  TV_HOST=<ip> HOME_KEY_SERVICE=1 STOCK_LAUNCHER=com.google.android.apps.tv.launcherx launcher/build-and-install.sh
  ```

  Keep `HOME_KEY_SERVICE=1`, or the gear button stops opening the panel. The build needs JDK 17 and
  `ANDROID_HOME=/opt/homebrew/share/android-commandlinetools`.
- Wallpaper and colours: push a theme from `themes/`, e.g. `TV_HOST=<ip> tools/theme.sh themes/aurora`;
  `tools/theme.sh --reset` goes back to `config.xml`. `themes/README.md` lists the keys.

## Notes

- With USB debugging on, port 5555 stays open on this firmware even after a reboot. Switch it off when done.
- Ad-blocking Private DNS was tried and turned off again: Nuvio buffered more with it.
- The picture settings page is the firmware's own Settings screen; neither launcher can restyle it.
