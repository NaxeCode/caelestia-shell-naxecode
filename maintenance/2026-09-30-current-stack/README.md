# September 30, 2026 shell refresh

Dated recipe and installation receipt, not a replacement for the root full-stack upgrade recipe.

Aladdin explicitly authorized installing and refreshing the monitor-menu cleanup.
Installed `caelestia-shell-naxecode 2.3.0.r39.5afb254a-1`, source commit
`5afb254a`, using a clean build against the current host libraries. The native
source is unchanged from installed source `34767588`; the intervening changes
are QML. This compatibility build derives from recipe commit `2aa86a3` and pins
Qt 6.11.1, Quickshell 0.3.0.r3.g7d1c9a9, libcava 0.10.7 and CLI 1.0.8. No
repository packages were upgraded or dependencies bypassed. A future desktop
stack upgrade must rebuild the private shell coherently with that stack; do not
retain these exact compatibility dependencies as a permanent version hold.

Build: makepkg cleanbuild, two parallel compiler jobs. Native shared-library
resolution passed. An isolated offscreen fixture loaded the staged Caelestia
modules and exercised the CPU service and Cava provider using the installed
Quickshell. QML syntax checks and intended menu/source checks passed.

Installed through pacman after native administrator authentication. The existing
systemd shell service was restarted under the user's explicit authorization.
The first IPC readiness probe timed out after five seconds; subsequent checks
confirmed a stable service, responsive IPC and loaded configuration. Visual
inspection confirmed Both monitors / OLED only, no Cintiq button, and live CPU,
GPU and room telemetry. Both monitors remained at 3840x2160/60 Hz. Package
verification reports 422 files and zero altered files. Layout-switch execution
was not tested because this refresh preserved the current dual-monitor setup.

The pre-update package and patched QML backup, complete build/install logs,
source/package hashes and post-restart receipt remain under the originating
Codex task's `work/shell-refresh-20260930/`. The retained package is
`caelestia-shell-naxecode-2.3.0.r35.34767588-1-x86_64.pkg.tar.zst`; restore its
compatible original libraries and receipted local QML together if rollback is
needed before any later native-stack change.

Package SHA256: `8a551fcb18b28e01cdad42a7f20ce8ab049adbc61ef1bf66e78c72bb73a303a7`.
