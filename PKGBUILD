# Maintainer: Mark Wagie <mark at manjaro dot org>
# Contributor: Fabio 'Lolix' Loli <fabio.loli@disroot.org> -> https://github.com/FabioLolix

pkgname=heroic-games-launcher
_app_id=com.heroicgameslauncher.hgl
pkgver=2.13.0
pkgrel=1
pkgdesc="Native GOG, Epic Games and Amazon games launcher for Linux"
arch=('x86_64')
url="https://heroicgameslauncher.com/"
license=('GPL-3.0-or-later')
depends=('alsa-lib' 'gtk3' 'nss')
makedepends=('node-gyp' 'yarn')
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/Heroic-Games-Launcher/HeroicGamesLauncher/archive/refs/tags/v${pkgver}.tar.gz")
sha256sums=('a40770bb23e0e6ad244361d51e76a571cffa0ec9bde8aa8b92be5ec403a89074')

prepare() {
  cd HeroicGamesLauncher-${pkgver}
  desktop-file-edit --set-key=Exec --set-value=heroic "flatpak/${_app_id}.desktop"
}

build() {
  cd HeroicGamesLauncher-${pkgver}
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

