# Maintainer: Mark Wagie <mark at manjaro dot org>
# Contributor: Fabio 'Lolix' Loli <fabio.loli@disroot.org> -> https://github.com/FabioLolix

pkgname=heroic-games-launcher
_app_id=com.heroicgameslauncher.hgl
pkgver=2.14.1
pkgrel=1
pkgdesc="Native GOG, Epic Games and Amazon games launcher for Linux"
arch=('x86_64')
url="https://heroicgameslauncher.com/"
license=('GPL-3.0-or-later')
depends=('alsa-lib' 'gtk3' 'nss')
makedepends=('node-gyp' 'yarn')
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/Heroic-Games-Launcher/HeroicGamesLauncher/archive/refs/tags/v${pkgver}.tar.gz")
sha256sums=('dc8d240a843af41a3d4d5d7ed5ca45f482f5a7fb7e7656016a91c6e133c35b22')

prepare() {
  cd HeroicGamesLauncher-${pkgver}
  desktop-file-edit --set-key=Exec --set-value=heroic "flatpak/${_app_id}.desktop"
}

build() {
  cd HeroicGamesLauncher-${pkgver}
  export YARN_CACHE_FOLDER="${srcdir}/yarn-cache"
  yarn
  yarn dist:linux tar.xz
}

package() {
  cd HeroicGamesLauncher-${pkgver}
  install -d "${pkgdir}/opt/heroic"
  cp -r dist/linux-unpacked/* "${pkgdir}/opt/heroic"

  install -d "${pkgdir}/usr/bin"
  ln -s /opt/heroic/heroic "${pkgdir}/usr/bin/"

  install -Dm644 public/icon.png "${pkgdir}/usr/share/icons/hicolor/1024x1024/apps/${_app_id}.png"
  install -Dm644 "flatpak/${_app_id}.png" -t "${pkgdir}/usr/share/icons/hicolor/128x128/apps/"
  install -Dm644 "flatpak/${_app_id}.desktop" -t "${pkgdir}/usr/share/applications/"
}

