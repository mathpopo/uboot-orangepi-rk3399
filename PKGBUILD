# U-Boot: Orange Pi RK3399/4
# Based on Arch Linux ARM package alarm/uboot-rock64
#
# Maintainer: Joseph Kogut <joseph.kogut@gmail.com>

buildarch=8

pkgname=uboot-orangepi-rk3399
pkgver=2020.01
_srcname=u-boot-${pkgver}
pkgrel=1
pkgdesc="U-Boot for Orange Pi RK3399/4"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
backup=('boot/boot.txt' 'boot/boot.scr')
makedepends=('bc' 'git')
install=${pkgname}.install
_commit_rkbin=5aa9a92b5652d37b323c870329103e120dfc8d87
source=("ftp://ftp.denx.de/pub/u-boot/${_srcname}.tar.bz2"
        "git+https://github.com/rockchip-linux/rkbin.git#commit=$_commit_rkbin"
        'boot.txt'
        'mkscr')
md5sums=('b6b2e0787b6874e6b57da0a065a84f5a'
         'SKIP'
         'e2869196adb467048d6e8c86fbf15fee'
         '021623a04afd29ac3f368977140cfbfd')

build() {
  cd ${_srcname}

  unset CLFAGS CXXFLAGS CPPFLAGS LDFLAGS

  make orangepi-rk3399_defconfig
  echo 'CONFIG_IDENT_STRING=" Arch Linux ARM"' >> .config
  make EXTRAVERSION=-${pkgrel}
}

package() {
  cd ${_srcname}

  mkdir -p "${pkgdir}/boot"

  tools/mkimage -n rk3399 -T rksd -d ../rkbin/bin/rk33/rk3399_ddr_933MHz_v1.24.bin "${pkgdir}/boot/idbloader.img"
  cat ../rkbin/bin/rk33/rk3399_miniloader_v1.24.bin >> "${pkgdir}/boot/idbloader.img"

  ../rkbin/tools/loaderimage --pack --uboot u-boot-dtb.bin "${pkgdir}/boot/uboot.img" 0x200000

  pushd ../rkbin
  tools/trust_merger ../rkbin/RKTRUST/RK3399TRUST.ini
  popd

  cp u-boot-dtb.bin ../rkbin/trust.img "${pkgdir}/boot"

  tools/mkimage -A arm -O linux -T script -C none -n "U-Boot boot script" -d ../boot.txt "${pkgdir}/boot/boot.scr"
  cp ../{boot.txt,mkscr} "${pkgdir}"/boot
}
