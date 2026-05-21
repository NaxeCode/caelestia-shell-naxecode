# Maintainer: NaxeCode <siraj.n.lee@gmail.com>
# Personal fork of caelestia-shell with OLED blackout mode for AW3225QF (DP-2).
# Reuses per-monitor `enabled:false` as a "render invisibly" flag — drawers
# remain instantiated on the screen so the launcher can render there, but every
# persistent paint surface (border, shadow, exclusion zones, bar left-stripe) is
# suppressed. Combined with hyprland misc.background_color=rgb(000000), the
# screen emits zero pixels at idle.

pkgname='caelestia-shell-naxecode'
_upstream_pkgname='caelestia-shell'
pkgver=1.6.1.r16.908707d1
pkgrel=1
pkgdesc='The desktop shell for the Caelestia dotfiles (NaxeCode fork — OLED blackout)'
arch=('x86_64')
url='https://github.com/NaxeCode/shell'
license=('GPL-3.0-only')
depends=('caelestia-cli' 'quickshell-git' 'ddcutil' 'brightnessctl' 'app2unit' 'libcava' 'networkmanager'
         'lm_sensors' 'fish' 'aubio' 'libpipewire' 'glibc' 'gcc-libs' 'ttf-material-symbols-variable' 'power-profiles-daemon'
         'ttf-rubik-vf' 'ttf-cascadia-code-nerd' 'swappy' 'libqalculate' 'bash' 'qt6-base' 'qt6-declarative')
makedepends=('cmake' 'ninja' 'git')
provides=("$_upstream_pkgname=$pkgver")
conflicts=("$_upstream_pkgname" "$_upstream_pkgname-git")
source=("git+https://github.com/NaxeCode/shell.git#branch=naxecode/oled-blackout")
sha256sums=('SKIP')

pkgver() {
    cd "${srcdir}/shell"
    # Format: <upstream-tag>.r<patch-count>.<short-sha>
    local last_tag
    last_tag="$(git describe --tags --abbrev=0 --match 'v*' 2>/dev/null | sed 's/^v//')"
    local commit_count
    commit_count="$(git rev-list --count "v${last_tag}..HEAD" 2>/dev/null || echo 0)"
    local short_sha
    short_sha="$(git rev-parse --short=8 HEAD)"
    printf '%s.r%s.%s' "$last_tag" "$commit_count" "$short_sha"
}

build() {
    cd "${srcdir}/shell"

    cmake -B build -G Ninja \
        -DCMAKE_BUILD_TYPE=RelWithDebInfo \
        -DCMAKE_INSTALL_PREFIX=/ \
        -DVERSION="$(git describe --tags --abbrev=0 --match 'v*' | sed 's/^v//')" \
        -DDISTRIBUTOR="NaxeCode fork (oled-blackout @ $(git rev-parse --short=8 HEAD))"
    cmake --build build
}

package() {
    cd "${srcdir}/shell"

    DESTDIR="$pkgdir" cmake --install build
    install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
