# Maintainer: Mark Wagie <mark at manjaro dot org>
# Contributor: Fabio 'Lolix' Loli <fabio.loli@disroot.org> -> https://github.com/FabioLolix

pkgname=heroic-games-launcher
_app_id=com.heroicgameslauncher.hgl
pkgver=2.22.1
pkgrel=1
_nodeversion=24
_electronversion=41
pkgdesc="Native GOG, Epic Games and Amazon games launcher for Linux"
arch=('x86_64')
url="https://heroicgameslauncher.com/"
license=('GPL-3.0-only')
depends=(
  "electron${_electronversion}"
  'rsync'
  'which'
)
makedepends=(
  'git'
  'imagemagick'
  'nvm'
  'pnpm'
  'python'
)
optdepends=(
  'gamemode: GameMode support'
  'gamescope: Gamescope support'
  'lib32-gamemode: GameMode support (32-bit)'
  'lib32-mangohud: MangoHud support (32-bit)'
  'mangohud: MangoHud support'
  'umu-launcher: Proton support'
)
source=("git+https://github.com/Heroic-Games-Launcher/HeroicGamesLauncher.git#tag=v$pkgver"
        'heroic.sh'
        'fix-exec-heroic.patch')
sha256sums=('cabb6f90e46f34f4af7388e3ab7c40ddd3502cda73fbeb145d40080d8f76d995'
            '965e1df4608e665cafa60d17b180d2522cd6241cfa9b50a21c3b2f2b389d97a0'
            'd97ad2117acda5e0b8b94728c571af43b54d405533d0cd2da99f397a0c948e50')

_ensure_local_nvm() {
  # let's be sure we are starting clean
  which nvm >/dev/null 2>&1 && nvm deactivate && nvm unload
  export NVM_DIR="${srcdir}/.nvm"

  # The init script returns 3 if version specified
  # in ./.nvmrc is not (yet) installed in $NVM_DIR
  # but nvm itself still gets loaded ok
  source /usr/share/nvm/init-nvm.sh || [[ $? != 1 ]]
}

prepare() {
  _ensure_local_nvm
  nvm install "${_nodeversion}"

  cd HeroicGamesLauncher
  export PNPM_HOME="$srcdir/pnpm-home"
  pnpm install --frozen-lockfile

  # Fix Add-to-Steam
  patch -Np1 -i $srcdir/fix-exec-heroic.patch

  # Set Exec
  desktop-file-edit --set-key=Exec --set-value="heroic %U" \
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
  _ensure_local_nvm
  pnpm download-helper-binaries
  pnpm electron-vite build
  pnpm electron-builder --linux --x64 --dir \
    ${dist} -c.electronDist=${electronDist} -c.electronVersion=${electronVer}

  # Generate icons
  for i in 16 32 48 64 128 256 512; do
    magick public/icon.png -resize "${i}x${i}" "public/icon_${i}x${i}.png"
  done
}

package() {
  cd HeroicGamesLauncher

  # Upstream requested installation in /opt/Heroic/ instead of /usr/lib/heroic
  install -Dm644 dist/linux-unpacked/resources/app.asar -t "$pkgdir/opt/Heroic/"
  cp -r dist/linux-unpacked/resources/app.asar.unpacked "$pkgdir/opt/Heroic"

  install -Dm755 "$srcdir/heroic.sh" "$pkgdir/usr/bin/heroic"

  for i in 16 32 48 64 128 256 512; do
    install -Dm644 public/icon_${i}x${i}.png \
      "$pkgdir/usr/share/icons/hicolor/${i}x${i}/apps/${_app_id}.png"
  done
  install -Dm644 public/icon.png \
    "${pkgdir}/usr/share/icons/hicolor/1024x1024/apps/${_app_id}.png"

  install -Dm644 "flatpak/${_app_id}.desktop" -t "${pkgdir}/usr/share/applications/"
}
