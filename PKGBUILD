# Maintainer: Filip Brcic <brcha@gna.org>
pkgname=mingw-w64-pthreads
pkgver=2.9.1
pkgrel=2
pkgdesc='MinGW-w64 pthreads library'
arch=('any')
url='http://sourceware.org/pthreads-win32/'
license=('LGPLv2+')
groups=('mingw-w64-toolchain' 'mingw-w64')
depends=()
makedepends=('mingw-w64-gcc-base' 'mingw-w64-binutils' 'mingw-w64-crt' 'mingw-w64-headers-bootstrap')
optdepends=()
provides=('mingw-w64-headers-bootstrap' 'mingw-w64-winpthreads')
conflicts=('mingw-w64-winpthreads')
replaces=('mingw-w64-winpthreads')
backup=()
options=('!strip' '!buildflags' '!libtool' '!emptydirs')
source=("ftp://sourceware.org/pub/pthreads-win32/pthreads-w32-${pkgver//./-}-release.tar.gz")
md5sums=('36ba827d6aa0fa9f9ae740a35626e2e3')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

build() {
  cd ${srcdir}/pthreads-w32-${pkgver//./-}-release

  export CFLAGS="-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions --param=ssp-buffer-size=4"
  export CXXFLAGS="-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions --param=ssp-buffer-size=4"
  unset LDFLAGS
    
  sed -e 's/-O3/-O3 -mms-bitfields/g' -i GNUmakefile

  for _arch in ${_architectures}
  do
    cp -av ${srcdir}/pthreads-w32-${pkgver//./-}-release ${srcdir}/build-${_arch}
    
    pushd ${srcdir}/build-${_arch}
    
    make clean
    make CROSS=${_arch}- GC-inlined
    make clean
    make CROSS=${_arch}- GCE-inlined
    mv libpthreadGC2.a libpthreadGC2.dll.a
    mv libpthreadGCE2.a libpthreadGCE2.dll.a
    make clean
    make CROSS=${_arch}- GC-static

    popd
  done
}

package() {
  cd ${srcdir}

  for _arch in ${_architectures}
  do
    pushd build-${_arch}

    mkdir -p ${pkgdir}/usr/${_arch}/{bin,lib,include}

    install -m 0755 *.dll ${pkgdir}/usr/${_arch}/bin
    install -m 0644 *.a ${pkgdir}/usr/${_arch}/lib
    install -m 0644 {pthread,sched,semaphore}.h ${pkgdir}/usr/${_arch}/include

    echo '/* dummy header */' > ${pkgdir}/usr/${_arch}/include/pthread_unistd.h
    echo '/* dummy header */' > ${pkgdir}/usr/${_arch}/include/pthread_time.h

    # Symlink libpthreadGC2.a to libpthread.a
    ln -s libpthreadGC2.a ${pkgdir}/usr/${_arch}/lib/libpthread.a
    ln -s libpthreadGC2.dll.a ${pkgdir}/usr/${_arch}/lib/libpthread.dll.a

    popd
  done
}
