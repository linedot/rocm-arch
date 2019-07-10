# Maintainer: Markus Näther <naetherm@informatik.uni-freiburg.de>
pkgname=hipcub
pkgver=2.6.0
pkgrel=1
pkgdesc="Reusable software components for the rocm developers."
arch=('x86_64')
url="https://github.com/ROCmSoftwarePlatform/hipCUB"
license=('NCSAOSL')
depends=(hcc hip)
makedepends=(git cmake gcc ninja hcc python2 rocminfo)
srcver="2.6.0"
source=("https://github.com/ROCmSoftwarePlatform/hipCUB/archive/$srcver.tar.gz")
sha256sums=("511011ac126b4213557b6faae64fbc2e5a4c6474aef5109cf57115344bf35546")

build() {
  mkdir -p "$srcdir/build"
  cd "$srcdir/build"

  # Tensile library needs python to be python2
  export PATH="$srcdir:$PATH"
  [[ -e "$srcdir/python" ]] || ln -s /usr/bin/python2 "$srcdir/python"

  # fix broken build with stack protection
  export CXXFLAGS=$(echo $CXXFLAGS | sed -e 's/-fstack-protector-strong//')
  export CFLAGS=$(echo $CFLAGS | sed -e 's/-fstack-protector-strong//')
  export CPPFLAGS=$(echo $CPPFLAGS | sed -e 's/-fstack-protector-strong//')

  # compile with HCC
  export CXX=/opt/rocm/hcc/bin/hcc

  # TODO: fix libhipcub.so, it contains references to $srcdir
  cmake -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX="$pkgdir/opt/rocm/hipcub" \
        -G Ninja \
        "$srcdir/hipCUB-$srcver"
  ninja
}

package() {
  ninja -C "$srcdir/build" install

  mkdir -p $pkgdir/etc/ld.so.conf.d
  cat <<-EOF > $pkgdir/etc/ld.so.conf.d/hipcub.conf
    /opt/rocm/hipcub/lib/
		EOF
}
