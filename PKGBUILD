# Maintainer: Mark Wagie <mark at manjaro dot org>
# Contributor: Fabio 'Lolix' Loli <fabio.loli@disroot.org> -> https://github.com/FabioLolix

pkgname=heroic-games-launcher
_app_id=com.heroicgameslauncher.hgl
pkgver=2.17.0
pkgrel=1
_electronversion=36
pkgdesc="Native GOG, Epic Games and Amazon games launcher for Linux"
arch=('x86_64')
url="https://heroicgameslauncher.com/"
license=('GPL-3.0-or-later')
depends=(
  "electron${_electronversion}"
  'which'
)
makedepends=(
  'git'
  'npm'
  'pnpm'
  'python'
)
source=("git+https://github.com/Heroic-Games-Launcher/HeroicGamesLauncher.git#tag=v$pkgver"
#source=("${pkgname}-${pkgver}.tar.gz::https://github.com/Heroic-Games-Launcher/HeroicGamesLauncher/archive/refs/tags/v${pkgver}.tar.gz"
        'heroic.sh')
sha256sums=('fd34c8a1fa0355618e0dec0139b31af9b9ef46c730c05fa360b5d1f107dc769d'
            'bdacef2303b6c6cb0b06cfe176494f1c34433c88f7f569f86c52a3f591824a8f')

prepare() {
  cd HeroicGamesLauncher

  # Set StartupWMClass
  desktop-file-edit --set-key=Exec --set-value=heroic --set-key=StartupWMClass --set-value=heroic \
    "flatpak/${_app_id}.desktop"

  sed -i "s|@ELECTRONVERSION@|${_electronversion}|" "$srcdir/heroic.sh"
}

build() {
  cd HeroicGamesLauncher
  export PNPM_HOME="$srcdir/pnpm-home"
  export PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD=1
  export ELECTRON_SKIP_BINARY_DOWNLOAD=1
  electronDist="/usr/lib/electron${_electronversion}"
  electronVer="$(sed s/^v// /usr/lib/electron${_electronversion}/version)"
  pnpm install
  pnpm download-helper-binaries
  pnpm electron-vite build
  pnpm electron-builder --linux --x64 --dir \
    ${dist} -c.electronDist=${electronDist} -c.electronVersion=${electronVer}
}

package() {
  cd HeroicGamesLauncher
  install -Dm644 dist/linux-unpacked/resources/app.asar -t "$pkgdir/usr/lib/heroic/"
  cp -r dist/linux-unpacked/resources/app.asar.unpacked "$pkgdir/usr/lib/heroic"

  install -Dm755 "$srcdir/heroic.sh" "$pkgdir/usr/bin/heroic"

  install -Dm644 public/icon.png "${pkgdir}/usr/share/icons/hicolor/1024x1024/apps/${_app_id}.png"
  install -Dm644 "flatpak/${_app_id}.png" -t "${pkgdir}/usr/share/icons/hicolor/128x128/apps/"
  install -Dm644 "flatpak/${_app_id}.desktop" -t "${pkgdir}/usr/share/applications/"
}

