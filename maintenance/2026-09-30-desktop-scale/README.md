# September 30, 2026 desktop scale controls

Source: `fdfb1a12`; package `caelestia-shell-naxecode 2.3.0.r42.fdfb1a12-1`.
Aladdin requested the scale control and explicitly authorized restarting the
shell when finished. This package targets the same qualified host stack as the
[System dashboard update](../2026-09-30-system-dashboard/README.md); dependencies
and native source are unchanged.

## Behavior and ownership

The System > Displays view adds Desktop scale presets for all active monitors:
100%, 125%, 150%, 167% (exact 5/3), and 200%. Each monitor card shows its current
scale. Values that do not yield integral logical pixels for every active output
are disabled; current 1.67 reports correctly select the 5/3 preset. Mixed scales
are represented by individual monitor labels without a falsely selected preset.

The existing `~/.local/bin/hypr-scale-toggle` remains the sole application path,
matching Super+Ctrl+Plus/Minus. Its wlr-randr transaction updates scale and
positions together while preserving unrelated display properties. This remains
**temporary zoom**: a display-settings reload or next login restores the saved
layout. Neither generated monitor-layout.conf nor its source policy was edited.

Scale changes have a busy state, error reporting and a timeout. Monitor state
refreshes immediately after a scale command and every two seconds while the
Displays view is open, so keyboard changes are reflected. Monitor delegates
retain their identities across refreshes through ScriptModel.

## Verification and rollout

- Scale validity, mixed states, exact 5/3 routing and missing readings passed the
  Node contract checks; previous power telemetry checks also passed.
- Native offscreen rendering passed at 760x650 and 360x500 with no text overflow.
- Native button-to-process tests dispatched the exact `1.666667` argument to a
  substitute helper and verified both success and error presentation.
- Previous hidden-view polling and malformed-reader recovery checks still pass.
- Packaged QML matches source. Staged native modules, CPU and Cava imports pass.
- Build uses two compiler jobs and the existing pinned dependency set; no
  dependency updates, package bypasses or desktop-overlay files were introduced.

The user's current 200% scale is preserved during rollout. Live scale changes
were not exercised during testing; action tests used a substitute helper.
Activation uses the existing systemd shell service, with independent checks of
service/IPC health, unchanged monitor modes and scales, and package integrity.
The final activation receipt is in the originating task's
`work/scale-shell-refresh-20260930/activation-result.json` alongside build,
installation and native-load logs.

Package SHA256: `cf6ecc932433e2bad65c5f5f6be6f472688f7a2b17c1bdfdcce03ef6c2911792`.
The compatible preceding package remains
`caelestia-shell-naxecode-2.3.0.r41.fc2226fa-1-x86_64.pkg.tar.zst` in the task's
`work/shell-refresh-20260930/` directory.
