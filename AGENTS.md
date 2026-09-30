# AGENTS.md

This repo is the Arch Linux packaging (`PKGBUILD`) for `caelestia-shell-naxecode`, a personal fork of caelestia-dots/shell (source: NaxeCode/shell, branch `naxecode/oled-blackout`) with OLED blackout and a System dashboard. The patched QML and native code live in the fork, not here. The root `PKGBUILD` is the full-stack recipe. `maintenance/<date>-<topic>/` holds dated recipes with their own pinned `PKGBUILD` and a README install receipt.

Build: `makepkg` (or `makepkg --cleanbuild`) in a clean environment against the complete intended Qt, Quickshell, libcava, and CLI stack. Lint with `namcap PKGBUILD`. There is no CI.

## Code Review Rules

Focus on build reproducibility, supply-chain integrity, and not breaking a live desktop. Formatting is not a review concern.

### Always flag (P0/P1)

- **Unpinned or unverified sources.**
  - New or edited dated recipes under `maintenance/` must pin `source=` to a full 40-character `#commit=<sha>`, not `#branch=` or `#tag=`, and the commit must match the one the README names.
  - Non-VCS sources (tarballs, patches, files) must have real `sha256sums` (or b2sums). `SKIP` is acceptable only for git sources.
  - Flag a git URL that changes to a host or owner other than `github.com/NaxeCode/shell`.
- **Root PKGBUILD pin drift.** The root README says a specific source commit is retained (currently `34767588`). Flag a PKGBUILD or README change that moves the commit, `pkgver`, or `pkgrel` without updating the other, and flag any content change that doesn't bump `pkgrel`.
- **Package identity.** Keep `provides=("caelestia-shell=$pkgver")` and `conflicts=(caelestia-shell caelestia-shell-git)`. Dropping either lets `paru -Syu` overwrite the fork or breaks `caelestia-cli` resolution.
- **Unsafe `package()` or `build()`.** Flag writes outside `$pkgdir`/`$srcdir`, `sudo`, network fetches during `build()`/`package()`, `curl | sh`, `systemctl` or user-session commands, and edits to `~`/`$HOME`. Packaging must not touch the running desktop.
- **Dependency and ABI mismatches.** Native modules link against Qt, Quickshell, and libcava, so dependency constraints must describe the stack the package was actually built and qualified against. Flag a Qt, Quickshell, or libcava bump without a rebuild note. Flag permanent exact-version holds copied from a dated compatibility recipe into the root recipe.

### Flag when relevant

- A dated recipe README that lacks the source commit, the package version, what was verified, a rollback path, or a package SHA256 when it claims an install.
- `pkgver()` changes that stop producing `<tag>.r<count>.<sha8>` or that could fail when tags are missing.
- Dropped `install -Dm644 LICENSE` or a `license=` change that doesn't match the upstream shell's license.
- Instructions that recommend `makepkg -si` against an older live stack, or killing and detaching a second shell instead of using the existing systemd shell service.

### Don't flag

- `sha256sums=('SKIP')` for the single git source. Integrity comes from the git commit.
- Different, pinned dependency versions in older `maintenance/` recipes. They are historical receipts.
- Prose style in dated receipts.
