# September 30, 2026 System dashboard

Source: `fc2226fa` (dashboard redesign `245b0073`, slider wiring `fc2226fa`).
Package: `caelestia-shell-naxecode 2.3.0.r41.fc2226fa-1`.
Status: installed and activated on September 30, 2026, at 03:05 Eastern after
Aladdin explicitly requested the restart. The independent acceptance receipt
passed: service stable, IPC ready, dashboard opened, monitor modes/scales
unchanged, and 426 packaged files with zero alterations.

This dated recipe targets the currently installed Qt 6.11.1, Quickshell
0.3.0.r3.g7d1c9a9, libcava 0.10.7 and Caelestia CLI 1.0.8 stack. It does not
replace the root full-stack upgrade recipe. Native source is unchanged; build
and staged native imports were checked against the host stack. Dependencies
were not upgraded or bypassed. Two compiler jobs were allowed.

## Behavior

- The bounded System dashboard reads schema-v2 `pp-status --json` snapshots.
- Power & room groups profile verification, calibrated Govee readings, estimated
  tower heat, CPU and GPU readings. Extra sensors expand within a scrolling view.
- Displays groups the existing layout, brightness, resolution, rate, VRR and HDR
  controls. The brightness sliders now use the custom control's `interaction`
  event. No display modes or power profiles were changed during testing.
- The reader preserves null values, retains the last valid snapshot on errors,
  times out reads, and only polls while a System tab is open.

## Verification

- Formatter/schema checks passed (`node tests/power-telemetry.test.mjs`).
- Native Quickshell service fixture passed: hidden view does not poll, showing it
  starts polling, hiding it stops polling, bad JSON and nonzero exits preserve
  the prior snapshot, and the reader recovers on the next valid result.
- Offscreen renders passed at 760x650, 600x530 and 360x500, including absent and
  stale room sensors, profile drift, expanded readings, and monitor controls.
  Text overflow probes passed; rendered panels were visually inspected.
- Both brightness controls dispatched a simulated change to their monitor
  objects. Fixtures do not alter real brightness or invoke topology helpers.
- Packaged QML matches source; native shared libraries resolve; packaged native
  modules, CPU service and Cava provider loaded successfully offscreen.
- Packaged file watching remains disabled. Activation must use the existing
  systemd service in an agreed maintenance window or the next login.

Package SHA256: `a62ea20f7a4a206790e28306cc608012c917b79d2d9ac4dac231a47b4f1f1d62`.

Build, package and verification receipts are in the originating task's
`work/shell-refresh-20260930/`; UI and service fixtures are in
`work/pp-shell-preview/`. The compatible rollback package remains
`caelestia-shell-naxecode-2.3.0.r39.5afb254a-1-x86_64.pkg.tar.zst`.
