# Maintainer: Jonathan Kotta <jpkotta@gmail.com>
# Maintainer: Jonathan Vetter <marchlynx@mail.de>
# Contributor: Andras Biro <bbandi86@gmail.com>
# Contributor: Alex Forencich <alex at alexforencich dot com>
# Contributor: schuay <jakob.gruber@gmail.com>
# Contributor: Brad Fanella <bradfanella@archlinux.us>
# Contributor: Corrado Primier <bardo@aur.archlinux.org>
# Contributor: danst0 <danst0@west.de>

# Optionaly download the toolchain after login at microchip manually and extract to root directory
#https://www.microchip.com/en-us/tools-resources/develop/microchip-studio/gcc-compilers
#https://ww1.microchip.com/downloads/Secure/en/DeviceDoc/avr8-gnu-toolchain-3.6.1.1752-linux.any.x86_64.tar.gz

# Optionaly download atmel-avr-gcc at microchip manually and extract to root directory
#https://ww1.microchip.com/downloads/Secure/en/DeviceDoc/3.6.2.zip

pkgname=avr-gcc-atmel
pkgver=5.4.0
_atmelver=3.6.2
pkgrel=1
pkgdesc="The GNU AVR Compiler Collection (from Atmel)"

arch=('x86_64' 'i686')
license=('GPL' 'LGPL' 'FDL' 'custom')
depends=('avr-binutils' 'gcc-libs' 'libmpc')
makedepends=('unzip')
optdepends=('avr-libc: Standard C library for Atmel AVR development')
provides=("avr-gcc=$pkgver")
conflicts=('avr-gcc')
options=('staticlibs' '!emptydirs' '!strip')
noextract=("https://ww1.microchip.com/downloads/Secure/en/DeviceDoc/${_atmelver}.zip")
source=("https://ww1.microchip.com/downloads/Secure/en/DeviceDoc/${_atmelver}.zip"
	"https://libisl.sourceforge.io/isl-0.15.tar.bz2")


# Every time the file is downloaded it has a different signature
# DO NOT call make package with "--skipchecksums", otherwise you risk the build fail
# Verified, working signatures:
md5sums=('2d6d8fae4439a9305fede68d56a63207'   # 3.6.2.zip 
	 '8428efbbc6f6e2810ce5c1ba73ecf98c')  # isl-0.15.tar.bz2
#sha512sums=('78b8c48d539be900e3d0b89f1b6956c952e2a62373adbd3bf93525699a71195a1db8a37503fecee05b0ba28f679fcf920d364242cc91abe654fa8c8e03b1843c' # 3.6.2.zip
#	     '1e27b7798f7428abcb5e9b2e3fbe3842fede54c03bbd7bd3cf83703e1e4cca7d95c51326ab90253fe55b38c002183e8e78dfbb4d2cf20b0aabe02443c8e7d50f') #  isl-0.15.tar.bz2


_target=avr

_builddir=build

_basedir=gcc

#_islver=0.24-4  # not compartible with gcc-5.4.0
#_islver=0.16.1  # may work, not tested
#_islver=0.15.0  # use exact isl-string of downloaded version!!!
_islver=0.15     # THIS is the tested version
#_islver=0.11.1  # may work, not tested


#### gcc-12.1.0 is tested to successfully compile avr-gcc-5.4.0 alias mcp-ver-3.6.2
##gcc -v
##Es werden eingebaute Spezifikationen verwendet.
##COLLECT_GCC=gcc
##COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-pc-linux-gnu/12.1.0/lto-wrapper
##Ziel: x86_64-pc-linux-gnu
##Konfiguriert mit: /build/gcc/src/gcc/configure --enable-languages=c,c++,ada,fortran,go,lto,objc,obj-c++ --enable-bootstrap --prefix=/usr --libdir=/usr/lib --libexecdir=/usr/lib --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=https://bugs.archlinux.org/ --with-linker-hash-style=gnu --with-system-zlib --enable-__cxa_atexit --enable-cet=auto --enable-checking=release --enable-clocale=gnu --enable-default-pie --enable-default-ssp --enable-gnu-indirect-function --enable-gnu-unique-object --enable-linker-build-id --enable-lto --enable-multilib --enable-plugin --enable-shared --enable-threads=posix --disable-libssp --disable-libstdcxx-pch --disable-werror --with-build-config=bootstrap-lto --enable-link-serialization=1
##Thread-Modell: posix
##Unterstützte LTO-Kompressionsalgorithmen: zlib zstd
##gcc-Version 12.1.0 (GCC)



prepare() {

# extract downloaded atmel-gcc zip file
  cd ${srcdir}
  unzip ${_atmelver}.zip

# delete not needed packages
  cd ${_atmelver}
  rm avr8-headers.zip
  rm avr-binutils.tar.bz2
  #rm avr-gcc.tar.bz2 ## needed to build avr-gcc
  rm avr-gdb.tar.bz2

  rm avr-libc.tar.bz2
  rm build-avr8-gnu-toolchain.sh
  rm SOURCES.README

## link files to root directory
#  cd ${srcdir}/../
#  ln -s ${srcdir}/${_atmelver} ./

# extract avr-gcc
  cd ${srcdir}
  echo "extract -> ${_atmelver}/avr-gcc.tar.bz2 mit bsdtar"
  bsdtar -x -f ${_atmelver}/avr-gcc.tar.bz2

# prepare package
  cd ${srcdir}/${_basedir}

  # https://bugs.archlinux.org/task/34629
  sed -i "/ac_cpp=/s/\$CPPFLAGS/\$CPPFLAGS -O2/" libiberty/configure gcc/configure

# optionally copy patches to source directory to leave out "../" in paths to patch
#  cp gperf-inlines.patch ${srcdir}/gperf-inlines.patch

# already applied patches:
##  patch -p0 < ${srcdir}/gperf-inlines.patch
# Patches for avr-gcc-5.0.1 are not needed anymore

# Fix incremention of boolean variable error in C++-17:
  patch -p1 < ${srcdir}/../reload1.c.patch

# Fix missing isl headers:
# source: https://github.com/SaberMod/GCC_SaberMod/commit/114e4e9470260a839d55aad2421fb646af12697b

#  patch -p0 < ${srcdir}/../graphite-interchange.c.patch
  patch -p1 < ${srcdir}/../graphite-optimize-isl.c.patch
  patch -p1 < ${srcdir}/../graphite-poly.h.patch

}

build() {
  cd ${srcdir}/${_basedir}
  rm -rf ${_builddir}
  mkdir -p ${_builddir}


  # remove link to old isl
  rm -f isl

# link isl for in-tree build
  ln -s ../isl-${_islver} isl  # currently used location
#  ln -s ../../isl-${_islver} isl
##  ln -s ../../isl-${_islver}/src/isl-${_islver} isl

  # https://bugs.archlinux.org/task/34629
  # hack! - some configure tests for header files using "$CPP $CPPFLAGS"
  sed -i "/ac_cpp=/s/\$CPPFLAGS/\$CPPFLAGS -O2/" {libiberty,gcc}/configure

#  echo ${pkgver} > gcc/BASE-VER


  # default CFLAGS lead to issues later on when configure
  # calls avr-gcc with -march set.
  export CFLAGS="-O2 -pipe"
  export CXXFLAGS="-O2 -pipe"

  config_guess=$(./config.guess)

  echo "cd _builddir"
  cd ${_builddir}

  echo "Add isl path to configure"
  # Add isl path to configure
#  . ${srcdir}/gcc/configure --with-isl=/usr/local/include/isl/


  # echo "CFLAGS"
  ## major differences from official avr build script (in version 5.0.1 only):
  ##   --disable-shared
  # export CFLAGS="-Os -g0 -s" ${srcdir}/gcc/configure \ # optimization for host gcc-12 only
  # echo ${CFLAGS}


    # --disable-linker-build-id   https://bugs.archlinux.org/task/34902
    # --disable-__cxa_atexit   https://bugs.archlinux.org/task/50848
    ${srcdir}/${_basedir}/configure \
                --disable-install-libiberty \
                --disable-libssp \
                --disable-libstdcxx-pch \
                --disable-libunwind-exceptions \
                --disable-linker-build-id \
                --disable-nls \
                --disable-werror \
                --disable-__cxa_atexit \
                --enable-checking=release \
                --enable-clocale=gnu \
                --enable-gnu-unique-object \
                --enable-gold \
                --enable-languages=c,c++ \
                --enable-ld=default \
                --enable-lto \
                --enable-plugin \
                --enable-shared \
                --infodir=/usr/share/info \
                --libdir=/usr/lib \
                --libexecdir=/usr/lib \
                --mandir=/usr/share/man \
                --prefix=/usr \
                --target=$_target \
                --with-as=/usr/bin/$_target-as \
                --with-gnu-as \
                --with-gnu-ld \
                --with-ld=/usr/bin/$_target-ld \
                --with-plugin-ld=ld.gold \
                --with-system-zlib \
                --with-isl \
                --enable-gnu-indirect-function

    make

# options used in avr-gcc-gnu version 11.0.1 (svn-to-git-repo)
# source: https://github.com/archlinux/svntogit-community/blob/master/avr-gcc/repos/community-x86_64/PKGBUILD
#/usr/local/gcc-5.2/gcc-5.2.0/configure --prefix=/usr/local/gcc-5.2/gcc-5.2.0/objdir 
#--enable-languages=c,c++ --enable-threads=posix --disable-multilib 
#--disable-nls --disable-bootstrap LD_LIBRARY_PATH=/usr/local/lib
}

package() {
    cd ${srcdir}/${_basedir}/${_builddir}

    make -j1 DESTDIR=${pkgdir} install

    # Strip debug symbols from libraries; without this, the package size balloons to ~500MB.
    find ${pkgdir}/usr/lib -type f -name "*.a" \
        -exec /usr/bin/$_target-strip --strip-debug '{}' \;

    # Install Runtime Library Exception
    install -Dm644 ${srcdir}/${_basedir}/COPYING.RUNTIME \
        ${pkgdir}/usr/share/licenses/$_target-gcc/RUNTIME.LIBRARY.EXCEPTION

    rm -r ${pkgdir}/usr/share/man/man7
    rm -r ${pkgdir}/usr/share/info
    rm ${pkgdir}/usr/lib/libcc1.*
}
