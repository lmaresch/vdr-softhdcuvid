# This PKGBUILD is part of the VDR4Arch project [https://github.com/vdr4arch]

# Maintainer: Christopher Reimer <mail+vdr4arch[at]c-reimer[dot]de>
pkgname=vdr-softhdcuvid
pkgver=1.1.0_4_g24f679e
_gitver=24f679e1d3f0eda895bb3fecbcd75ec2e3aeb869
_vdrapi=2.4.0
pkgrel=3
pkgdesc="software and GPU emulated HD output device plugin for VDR"
url="http://projects.vdr-developer.org/projects/plg-softhddevice"
arch=('x86_64' 'i686' 'arm' 'armv6h' 'armv7h')
license=('AGPL3')
depends=('ffmpeg' 'freeglut' 'glew' 'mesa' "vdr-api=${_vdrapi}" 'xcb-util-wm' 'xorg-server' 'cuda' 'nvidia>=410.48' 'libplacebo>=1.7.0')
optdepends=('vdr-xorg: Recommended way to start X.org server together with VDR')
makedepends=('git' 'glm' 'glu' 'patchutils')
_plugname=${pkgname//vdr-/}
source=("vdr-plugin-${_plugname}::git+https://github.com/jojo61/vdr-plugin-softhdcuvid#commit=$_gitver"
        "50-$_plugname.conf"
        "vdr-plugin-softhdcuvid-fix-DrawPixel-error.patch"
        "vdr-plugin-softhdcuvid-fix-__checkCudaErrors.patch"
        "vdr-plugin-softhdcuvid-fix-compilerwarnings.patch")
backup=("etc/vdr/conf.avail/50-$_plugname.conf")
sha512sums=('SKIP'
         '0b336e5d0c18e5a875389c52d498ce81db0a407c6a93e1e72e0d0faec41d2165b80e91e9787465bb2cb9923ca65e6ce50e4a086f0d26410059899fef2bbe87b0'
         'fe0f5ddc0d8086a350ea3c02083253578a6de4e88a616059c86be6dd42510c795a50f060e9c29dcfb6208ebedd41e38257afb9857049dd3fbc01b70bc6c684cb'
         'cbc189ef27a0f181ba02e16c68669c2a0367fc4261b6151ca87946057df78811792dc595edd5d1cbb8590b3e8d402126e985acd9f83935a87ef709d79a68c479'
         '7eb65afceb41634c2ff6712464e9b56379efee480fdb4d2192ab218c56242a1f9b1f63e15bf6c729ac6eb08c6673a1464857bb2118d007b5b525d7029365cd21')

pkgver() {
  cd "${srcdir}/vdr-plugin-${_plugname}"
  git describe --tags | sed 's/-/_/g;s/v//'
}

prepare() {
  cd "${srcdir}/vdr-plugin-${_plugname}"

  # Disable OSS. Arch Linux doesn't ship OSS
  sed -i '/OSS /d' Makefile

  # Enable libplacebo
  sed -i 's/LIBPLACEBO=0/LIBPLACEBO=1/' Makefile

  # Adapt path to cuda headers, in arch they are installed in /opt/cuda/include
  sed -i 's%/usr/local/cuda/include%/opt/cuda/include%' Makefile

  # Adapt path to cuda libraries, in arch the are installed in /opt/cuda/lib64
  sed -i 's%-L/usr/local/cuda/targets/x86_64-linux/lib%-L/opt/cuda/lib64%' Makefile

  # Fix DrawPixel log errors with some skins
  patch -Np1 -i "${srcdir}/vdr-plugin-softhdcuvid-fix-DrawPixel-error.patch"

  # Fix issue with undefined symbol: __checkCudaErrors, patch from Ulrich Eckhardt - https://www.vdr-portal.de/forum/index.php?thread/129888-softhddevice-with-hevc-and-uhd/&postID=1310461#post1310461
  patch -Np1 -i "${srcdir}/vdr-plugin-softhdcuvid-fix-__checkCudaErrors.patch"

  # Fix some unused-variables warnings, based on patch from Ulrich Eckhardt - https://www.vdr-portal.de/forum/index.php?thread/129888-softhddevice-with-hevc-and-uhd/&postID=1310461#post1310461
  patch -Np1 -i "${srcdir}/vdr-plugin-softhdcuvid-fix-compilerwarnings.patch"
}

build() {
  cd "${srcdir}/vdr-plugin-${_plugname}"
  make
}

package() {
  cd "${srcdir}/vdr-plugin-${_plugname}"
  PKG_CONFIG_PATH="/usr/lib/ffmpeg2.8/pkgconfig" make DESTDIR="${pkgdir}" install

  install -Dm644 "$srcdir/50-$_plugname.conf" "$pkgdir/etc/vdr/conf.avail/50-$_plugname.conf"
}