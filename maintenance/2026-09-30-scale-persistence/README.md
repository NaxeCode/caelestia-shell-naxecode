# Desktop scale persistence — September 30, 2026

Shell source `52033e721a0ac3a760b2548cde0b78cc3b52923b` changes the scale caption to match the installed persistent
helper. Dotfiles commit `1c8862c` owns persistence, per-display preferences,
reload/login restoration, incompatible-resolution fallback, and six regression
tests (27 assertions). Live 167% was saved and the actual login restoration
command preserved it; no OS reboot was performed. Mode, 60 Hz refresh, bit depth,
VRR and neighboring positions remained intact, with no compositor config errors.

This package uses the same qualified Qt/Quickshell stack as the previous desktop
scale package; no dependency upgrades or native-source changes. Install without
restarting the active shell: the helper fix works immediately and the updated
caption loads at the next normal shell start. Preserve the package-owned whole
directory symlink and never add per-file overlays.

Built package SHA256:
`2c268a9a334d7b81c7793ade1624fed5c33128f438b48daeaa26b1b801a6e9b6`.
The package installation requires local administrator authentication; do not
claim the caption is loaded merely because the helper is active. Installation
and activation status live in the originating task's scale-persistence receipt.
