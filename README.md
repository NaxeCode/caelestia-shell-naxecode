# caelestia-shell-naxecode

Arch Linux PKGBUILD that builds [NaxeCode/shell](https://github.com/NaxeCode/shell) — a personal fork of [caelestia-dots/shell](https://github.com/caelestia-dots/shell) with an OLED blackout mode for the Dell AW3225QF (DP-2).

## What it does

Reuses the per-monitor `enabled: false` flag in `~/.config/caelestia/monitors/<NAME>/shell.json` as a "render invisibly" trigger:

- The drawer layer-shell window stays instantiated on the screen, so launcher / dashboard / sidebar / etc. still render there when invoked
- Every persistent paint surface is suppressed: border outline, drop shadow, exclusion-zone strips, bar left-edge thickness column
- Combined with `misc.background_color = rgb(000000)` in `hyprland.conf` and a black wallpaper, the screen emits zero pixels at idle

Designed for an OLED panel that's physically connected and used by Hyprland for windows, but should accumulate zero burn-in time when not actively displaying content.

## Install

```bash
git clone https://github.com/NaxeCode/caelestia-shell-naxecode.git
cd caelestia-shell-naxecode
makepkg -si
```

Conflicts with `caelestia-shell` and `caelestia-shell-git` from AUR — pacman will prompt to remove them. Provides `caelestia-shell` so dependent packages (e.g. `caelestia-cli`) remain satisfied.

## Upgrade workflow

When upstream caelestia ships a new version:

```bash
cd ~/NaxeTools/shell                       # the fork clone
git fetch upstream
git rebase upstream/main                    # carry the OLED patches forward
git push --force-with-lease origin naxecode/oled-blackout

cd ~/NaxeTools/caelestia-shell-naxecode
makepkg -f                                  # pkgver() auto-bumps from git describe
sudo pacman -U *.pkg.tar.zst
caelestia shell -k && caelestia shell -d
```

## Files

- `PKGBUILD` — sources from the fork's `naxecode/oled-blackout` branch via git+https
- The patched QML files live in the fork repo, not here:
  - `modules/drawers/Drawers.qml` — iterate `Quickshell.screens` (not the filtered `Screens.screens`)
  - `modules/drawers/ContentWindow.qml` — gate `borderThickness` / `borderRounding` / `shadowOpacity` / `BlobInvertedRect.visible` on `oledBlackout`
  - `modules/drawers/Exclusions.qml` — gate `ExclusionZone.visible` on `oledBlackout`
  - `modules/bar/BarWrapper.qml` — `implicitWidth = 0` when `disabled`, regardless of `Config.border.thickness`
  - `modules/areapicker/AreaPicker.qml` — iterate `Quickshell.screens` so SUPER+Z screenshot picker instantiates on blacked-out monitors
  - `services/SysControl.qml` — singleton that polls `pp-data` JSON every 2s and watches `hyprland.conf` via FileView; exposes profile / monitor-mode / CPU / GPU / Govee state to the dashboard
  - `modules/dashboard/SystemTab.qml` — new "System" dashboard tab: pp-* power-profile + mon-* layout segmented controls, per-monitor refresh-rate / VRR / HDR toggles (all driven dynamically from `Hypr.monitors.lastIpcObject`, no hardcoded connector names), telemetry tile, "Open pp-status" launcher
  - `modules/dashboard/Content.qml` — registers the new System tab in `dashboardTabs`

## Why a separate package name

The AUR ships `caelestia-shell` and `caelestia-shell-git`. Naming this fork `caelestia-shell-naxecode` with `provides=(caelestia-shell)` and `conflicts=(caelestia-shell caelestia-shell-git)` means:

- `paru -Syu` won't try to overwrite our patches with the AUR build
- Anything depending on `caelestia-shell` (e.g. `caelestia-cli`) still resolves cleanly
- Easy to switch back to upstream by `paru -S caelestia-shell` (which removes our package via the conflict)
