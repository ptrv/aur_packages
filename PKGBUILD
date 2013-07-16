# Maintainer: Bernardo Barros <bbarros@xsounds.org>

pkgname=sc3-plugins-git
pkgver=20130601
pkgrel=1
pkgdesc="Plugins for SuperCollider (Git version)"
url="http://supercollider.sourceforge.net/"
arch=('i686' 'x86_64')
license=('GPL')
depends=('supercollider>=3.6')
conflicts=('supercollider-with-extras-git')
provides=('supercollider-with-extras-git')

source=('git://sc3-plugins.git.sourceforge.net/gitroot/sc3-plugins/sc3-plugins')
md5sums=('SKIP')

_gitname="sc3-plugins"

pkgver() {
  # cd $srcdir/$_gitname
  # #git describe --always | sed 's|-|.|g'
  # echo $(git rev-list --count HEAD).$(git rev-parse --short HEAD)
  # date +%Y%m%d
  git log -1 --format="%cd" --date=short | tr -d '-'
}

prepare() {
  cd "$srcdir/$_gitname"

  msg "Update submodules..."
  git submodule sync
  git submodule update --init

  msg "Patch TagSystemUgens..."
  # fix TagSystemUgens
  sed -i "s/int m_count;/float m_count;/" source/TagSystemUGens/TagSystemUgens.cpp

}
build() {

  cd "$srcdir/$_gitname"

  mkdir build
  cd build

  cmake .. -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release \
    -DSC_PATH=$HOME/src/supercollider -DSUPERNOVA=1 -DAY=1

  make

}

package() {

  cd "$srcdir/$_gitname/build"

  make DESTDIR=$pkgdir/ install
}
