# Airwindows (Curated) for Ableton Move / Schwung

A trimmed-down fork of [charlesvestal/schwung-airwindows](https://github.com/charlesvestal/schwung-airwindows)
that lets you (and anyone else) show only the Airwindows plugins you
actually want in the Move's effect browser, instead of all 509 — no
recompiling required after the first install.

## Why this exists

The original module exposes all 509 Airwindows effects, which is
amazing but unwieldy to scroll through on a 4-line hardware display.
This fork adds a simple **allowlist file**: drop a plain text file with
the plugin names you want next to the bundled `Airwindows.clap`, and
only those show up. Everyone gets their own curated set without
touching any code.

## Quick start (just want to use it, no editing)

1. Download the latest release from the **Releases** page of this repo
   (`clap-module.tar.gz`).
2. Install it on your Move (requires [Schwung / Move Everything](https://github.com/charlesvestal/move-everything)
   running with SSH enabled at `http://move.local/development/ssh`):
   ```bash
   tar -xzf clap-module.tar.gz
   scp -r clap/* ableton@move.local:/data/UserData/move-anything/modules/audio_fx/clap/
   ```
   Or use **Schwung Manager → Files** in your browser (`http://move.local:7700`)
   and drag-and-drop the extracted folder contents instead of using SSH.
3. Restart Move Everything (or reboot the Move). Done — you'll see the
   curated list bundled in this release (currently tuned for **boom
   bap + ambient** production).

## Picking your own plugins (no compiling, no GitHub needed)

This is the whole point of this fork: once the module is installed,
changing which plugins show up is just editing a text file on the
device itself.

1. Open **Schwung Manager** in your browser: `http://move.local:7700`
2. Go to **Files** → navigate to:
   `data/UserData/schwung/modules/audio_fx/clap/plugins/`
3. You'll see `Airwindows.clap` and `allowlist.txt` sitting next to
   each other. Open/edit `allowlist.txt` directly in that interface
   (or download it, edit on your computer, and re-upload it).
4. Format: one plugin name per line, no prefix (e.g. `ToTape9`, not
   `Airwindows: ToTape9`). Lines starting with `#` are comments and
   are ignored. See the full list of all 509 names in the
   [original README](https://github.com/charlesvestal/schwung-airwindows#the-effects)
   to know what's available.
5. Save, then restart Move Everything (or reboot) so it rescans the
   plugins folder.

That's it — no fork, no Docker, no GitHub Actions. Anyone who installs
this module can curate their own list this way, independently of
whatever list shipped in the release.

If you'd rather skip the SFTP/Schwung Manager UI step entirely, you
can also SSH in directly:
```bash
ssh ableton@move.local
nano /data/UserData/move-anything/modules/audio_fx/clap/plugins/allowlist.txt
```

## Changing the *default* list this repo ships (for maintainers / forks)

If you want to publish your own curated release (not just edit your
own device), edit [`plugins/allowlist.txt`](plugins/allowlist.txt) in
this repo, bump the version in `src/module.json`, and push a tag
matching that version (e.g. `v0.2.0`). GitHub Actions
(`.github/workflows/release.yml`) builds the module via Docker and
attaches `clap-module.tar.gz` to the release automatically — no local
build environment needed.

```bash
git clone https://github.com/<you>/schwung-airwindows.git
cd schwung-airwindows
# edit plugins/allowlist.txt and src/module.json (bump "version")
git add -A && git commit -m "Update curated plugin list"
git push origin main
# then: GitHub → Releases → Create a new release → tag vX.Y.Z → Publish
```

If you prefer building locally instead of waiting on CI, the usual
flow from the original repo still works (requires Docker):
```bash
./scripts/build.sh
./scripts/install.sh
```

## How filtering works (for the curious / for contributors)

`src/dsp/clap_host.c` reads `plugins/allowlist.txt` once at scan time
(`load_allowlist()`). If the file is missing or empty, **all** plugins
are shown — this fork is fully backwards-compatible with the upstream
behavior. If it has entries, only plugins whose name matches a line in
the list (case-insensitive) are exposed to the UI
(`is_plugin_allowed()`). This means:

- No `Airwindows.clap` rebuild is ever needed to change your subset.
- The filtering logic itself only needs to be compiled once — after
  that, every change is just editing a text file on the device.

## Credits

Airwindows is the work of Chris Johnson — [airwindows.com](https://www.airwindows.com/).
The plugin build comes from [airwin2rack](https://github.com/baconpaul/airwin2rack)
by Paul Walker. The base Schwung module is by
[charlesvestal](https://github.com/charlesvestal/schwung-airwindows).
This fork (allowlist filtering) is MIT licensed, same as upstream.
