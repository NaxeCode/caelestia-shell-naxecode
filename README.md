<img src=".github/brand/logo.svg" width="80" alt="" />

# caelestia-shell-naxecode

Arch Linux PKGBUILD that builds [NaxeCode/shell](https://github.com/NaxeCode/shell), a personal fork of [caelestia-dots/shell](https://github.com/caelestia-dots/shell) with an OLED blackout mode for the Dell AW3225QF (DP-2).

![status](https://img.shields.io/badge/status-active-a7c080?style=flat&labelColor=2d353b)
![Arch Linux](https://img.shields.io/badge/Arch_Linux-7fbbb3?style=flat&labelColor=2d353b&logo=archlinux&logoColor=d3c6aa)
![Hyprland](https://img.shields.io/badge/Hyprland-7fbbb3?style=flat&labelColor=2d353b&logo=hyprland&logoColor=d3c6aa)
![Quickshell](https://img.shields.io/badge/Quickshell_%2F_QML-7fbbb3?style=flat&labelColor=2d353b&logo=qt&logoColor=d3c6aa)

## What it does

Reuses the per-monitor `enabled: false` flag in `~/.config/caelestia/monitors/<NAME>/shell.json` as a "render invisibly" trigger:

- The drawer layer-shell window stays instantiated on the screen, so launcher / dashboard / sidebar / etc. still render there when invoked
- Every persistent paint surface is suppressed: border outline, drop shadow, exclusion-zone strips, bar left-edge thickness column
- Combined with `misc.background_color = rgb(000000)` in `hyprland.conf` and a black wallpaper, the screen emits zero pixels at idle

Designed for an OLED panel that's physically connected and used by Hyprland for windows, but should accumulate zero burn-in time when not actively displaying content.

## How it works

### Files

- `PKGBUILD` — sources from the fork's `naxecode/oled-blackout` branch via git+https
- The patched QML files live in the fork repo, not here:
  - `modules/drawers/Drawers.qml` — iterate `Quickshell.screens` (not the filtered `Screens.screens`)
  - `modules/drawers/ContentWindow.qml` — gate `borderThickness` / `borderRounding` / `shadowOpacity` / `BlobInvertedRect.visible` on `oledBlackout`
  - `modules/drawers/Exclusions.qml` — gate `ExclusionZone.visible` on `oledBlackout`
  - `modules/bar/BarWrapper.qml` — `implicitWidth = 0` when `disabled`, regardless of `Config.border.thickness`
  - `modules/areapicker/AreaPicker.qml` — iterate `Quickshell.screens` so SUPER+Z screenshot picker instantiates on blacked-out monitors
  - `services/SysControl.qml` — singleton that reads schema-v2 `pp-status --json` every 2s while the System tab is open; preserves unknown readings, reports stale data and watches monitor configuration
  - `modules/dashboard/SystemTab.qml` — bounded "System" dashboard with Power & room and Displays views, expandable sensor readings, responsive monitor controls and a terminal view
  - `modules/dashboard/Content.qml` — registers the new System tab in `dashboardTabs`

### Why a separate package name

The AUR ships `caelestia-shell` and `caelestia-shell-git`. Naming this fork `caelestia-shell-naxecode` with `provides=(caelestia-shell)` and `conflicts=(caelestia-shell caelestia-shell-git)` means:

- `paru -Syu` won't try to overwrite our patches with the AUR build
- Anything depending on `caelestia-shell` (e.g. `caelestia-cli`) still resolves cleanly
- Easy to switch back to upstream by `paru -S caelestia-shell` (which removes our package via the conflict)

## Getting started

Build against the complete intended Qt, Quickshell, shell-native-library and CLI
stack in a resource-bounded clean environment. Do not use `makepkg -si` to pull
new dependencies into an older active desktop.

```bash
git clone https://github.com/NaxeCode/caelestia-shell-naxecode.git
cd caelestia-shell-naxecode
makepkg
```

The September 2026 qualified rebuild retains source `34767588` and increments
the package release to 3 for libcava 1.0.0/SONAME 1 and Caelestia CLI 1.1.2. It
was exercised with Qt 6.11.2 and Quickshell `0.3.1.r10.g2d3b3e9`; it is not a
QML-only replacement or permission to install one component independently.

Conflicts with `caelestia-shell` and `caelestia-shell-git`; provides
`caelestia-shell`. Review the exact replacement rather than using a wildcard
package install.

### Upgrade workflow

Review upstream changes against the actual retained capabilities before changing
the fork. Do not automatically rebase and force-push the branch. Build the
toolkit and native shell together against the intended complete dependency set;
exercise real native consumers and preserve compatible previous packages/config.

Install the selected package only as part of a reviewed coherent full transaction
with independent recovery and an explicit maintenance window. `paru -Syu` does
not rebuild this private package when Qt or libcava changes. The workstation
owner documents its repository-specific full-update targets and boot constraints.
Use the existing systemd shell owner for activation; do not kill and detach a
second shell beneath active work. Native display, idle and input acceptance
remains necessary after the restart.

## Current-stack System dashboard

The [September 30 System dashboard recipe](maintenance/2026-09-30-system-dashboard/README.md)
records the pp-status integration and its checks against the currently installed
host stack. The [desktop scale update](maintenance/2026-09-30-desktop-scale/README.md)
adds the existing accessibility scale ladder to the Displays view. These dated
recipes are separate from the full-stack recipe above.

## Status

In daily use on one Hyprland workstation. Package release 3 tracks fork commit `34767588` (35 commits on top of upstream v2.3.0). Not published to the AUR.

## How this project is run

[![Tracked in Linear](https://img.shields.io/badge/tracked_in-Linear-5e6ad2?style=flat&labelColor=2d353b&logo=linear&logoColor=d3c6aa)](https://linear.app)
[![AI code review](https://img.shields.io/badge/code_review-Codex-7fbbb3?style=flat&labelColor=2d353b&logo=openai&logoColor=d3c6aa)](AGENTS.md)
[![main is PR-only](https://img.shields.io/badge/main-PR--only-a7c080?style=flat&labelColor=2d353b&logo=github&logoColor=d3c6aa)](#how-this-project-is-run)

- **Planning:** work is tracked in Linear as initiatives → projects → milestones → issues; branches and PR titles carry the issue ID so status moves automatically from In Progress to Done.
- **Review:** every pull request gets an automatic Codex review guided by this repo's own Code Review Rules in [`AGENTS.md`](AGENTS.md), and review threads must be resolved before merge.
- **Guardrails:** the default branch (`main`) only changes through pull requests — no direct pushes or force-pushes.

## License

MIT. See [LICENSE](LICENSE).

---
<sub>Built by [Aladdin Ali](https://github.com/NaxeCode) · [naxecode.github.io](https://naxecode.github.io)</sub>
