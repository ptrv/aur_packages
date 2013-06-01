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

# SC3-plugins:
_gitroot="git://sc3-plugins.git.sourceforge.net/gitroot/sc3-plugins/sc3-plugins"
_gitname="sc3-plugins"


build() {

  if [ -d $_gitname ] ; then
    (cd $_gitname && git pull origin && git submodule update)
    msg "The local files are updated."
  else
    git clone --recursive $_gitroot
  fi

  msg "GIT checkout done or server timeout"
  msg "Starting make..."

  rm -rf $_gitname-build

  cd $_gitname
  git submodule update --init
  cd ..

  # fix TagSystemUgens
  sed -i "s/int m_count;/float m_count;/" $_gitname/source/TagSystemUGens/TagSystemUgens.cpp

  cp -r $_gitname $_gitname-build
  cd $_gitname-build


  cmake . -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release \
    -DSC_PATH=$HOME/src/supercollider -DSUPERNOVA=1 -DAY=1
  make

  cd ..
  cd $_gitname
  git checkout .
}

package() {

  cd $_gitname-build

  make DESTDIR=$pkgdir/ install
}
