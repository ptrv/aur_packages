# Maintainer:  Bernardo Barros <bernardobarros@gmail.com>
# Contributor: Bj�rn Lindig <bjoern.lindig@googlemail.com>

pkgname=supercollider-3.6-git
pkgver=20130601
pkgrel=1
pkgdesc="An environment and programming language for real time audio synthesis and algorithmic composition (3.6 branch)."
url="http://supercollider.sourceforge.net/"
arch=('i686' 'x86_64')
license=('GPL')
depends=('jack' 'fftw' 'avahi' 'ruby' 'icu' 'cwiid' 'rsync' 'qtwebkit')
makedepends=('git' 'libsndfile>=1.0' 'emacs' 'vim' 'gedit' 'pkgconfig>=0.14.0' 'cmake' 'alsa-lib'
'boost')
optdepends=('sc3-plugins-git: extra audio UGens'
            'swingosc: java based GUI system')
conflicts=('supercollider')
provides=('supercollider=3.6')
# source=("0001-cmake-link-pthreads-libraries.patch")
# md5sums=('dd6c3bd6c67cf14082124fce8a7bc70c')

install=sc3.install

# Official git repo
_gitroot="git://github.com/supercollider/supercollider.git"
_gitname="supercollider"

build() {

  export LDFLAGS="${LDFLAGS//-Wl,--as-needed}"

  cd $srcdir
  msg "Connecting to GIT server...."

  if [ -d $_gitname ] ; then
    cd $_gitname
    git pull origin
    git submodule sync
    git submodule update --init
    cd ..
    msg "The local files are updated."
  else
    git clone $_gitroot
    cd $_gitname
    git checkout -t origin/3.6
    git submodule sync
    git submodule update --init
    # git apply --stat ../0001-cmake-link-pthreads-libraries.patch
    cd ..
  fi

  msg "GIT checkout done or server timeout"
  msg "Starting make..."
  rm -rf $_gitname-build

  cp -r $_gitname $_gitname-build
  cd $_gitname-build/

  mkdir build
  cd build

  cmake .. -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release \
    -DSC_WII=1 -DSUPERNOVA=1 -DSC_QT=1 # -DNATIVE=1

  # -DSSE=1 -DSSE41=1 -DSSE42=1

  make
}

package() {

  cd $srcdir/$_gitname-build/build

  make DESTDIR=$pkgdir/ install
}
