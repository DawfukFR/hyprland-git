# Maintainer: FabioLolix
# Maintainer: éclairevoyant
# Contributor: ThatOneCalculator <kainoa at t1c dot dev>

pkgname=hyprland-git
pkgver=0.40.0.r160.c95845b1
pkgrel=1
pkgdesc="A dynamic tiling Wayland compositor based on wlroots that doesn't sacrifice on its looks."
arch=(x86_64 aarch64)
url="https://github.com/hyprwm/Hyprland"
license=(BSD)
depends=(
  cairo
  cpio
  gcc-libs
  glib2
  glibc
  glslang
  hyprutils
  hyprcursor
  hyprlang
  hyprwayland-scanner
  libdisplay-info
  libdrm
  libglvnd
  libinput
  libliftoff
  libx11
  libxcb
  libxcomposite
  libxfixes
  libxkbcommon
  libxrender
  mesa
  opengl-driver
  pango
  pixman
  polkit
  seatd
  systemd-libs
  tomlplusplus
  wayland
  wayland-protocols
  xcb-proto
  xcb-util
  xcb-util-errors
  xcb-util-image
  xcb-util-keysyms
  xcb-util-renderutil
  xcb-util-wm
  xorg-xinput
  xorg-xwayland
)
depends+=(libdisplay-info.so)
makedepends=(
  cmake
  gdb
  git
  jq
  make
  meson
  ninja
  patch
  pkgconf
  xorgproto
)
provides=("hyprland=${pkgver%%.r*}")
conflicts=(hyprland)
source=(
  "git+https://github.com/hyprwm/Hyprland.git"
  "git+https://github.com/hyprwm/wlroots-hyprland.git"
  "git+https://github.com/hyprwm/hyprland-protocols.git"
  "git+https://github.com/canihavesomecoffee/udis86.git"
  "git+https://github.com/wolfpld/tracy.git"
)
b2sums=(
  'SKIP'
  'SKIP'
  'SKIP'
  'SKIP'
  'SKIP'
)

pick_mr() {
  git pull origin pull/$1/head --no-edit
}

prepare() {
  cd Hyprland
  git submodule init
  git config submodule.subprojects/wlroots-hyprland.url "$srcdir/wlroots-hyprland"
  git config submodule.subprojects/hyprland-protocols.url "$srcdir/hyprland-protocols"
  git config submodule.subprojects/udis86.url "$srcdir/udis86"
  git config submodule.subprojects/tracy.url "$srcdir/tracy"
  git -c protocol.file.allow=always submodule update

  if [[ -z "$(git config --get user.name)" ]]; then
    git config user.name local && git config user.email '<>' && git config commit.gpgsign false
  fi
  # Pick pull requests from github using `pick_mr <pull request number>`.

  git -C subprojects/wlroots-hyprland reset --hard
}

pkgver() {
  git -C Hyprland describe --long --tags | sed 's/^v//;s/\([^-]*-\)g/r\1/;s/-/./g'
}

build() {
  cd Hyprland

  meson setup build \
    --prefix     /usr \
    --libexecdir lib \
    --sbindir    bin \
    --buildtype  release \
    --wrap-mode  nodownload \
    -D           b_lto=true \
    -D           b_pie=true \
    -D           default_library=shared \
    -D           xwayland=enabled \
    -D           systemd=enabled

  meson compile -C build
}

package() {
  cd Hyprland

  meson install -C build \
    --destdir "$pkgdir"
  
  # FIXME: remove after xdg-desktop-portal-hyprland disowns hyprland-portals.conf
  rm -rf "$pkgdir/usr/share/xdg-desktop-portal"

  # license
  install -Dm0644 -t "$pkgdir/usr/share/licenses/${pkgname}" LICENSE
}
# vi: et ts=2 sw=2
