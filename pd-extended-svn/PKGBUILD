# Maintainer: <fero dot kiraly at gmail.com>

pkgname=pd-extended-svn
pkgver=0.43.4
pkgrel=1
pkgdesc="Pure Data Extended SVN version"
url="http://puredata.info/"
arch=('i686' 'x86_64' )
license=('BSD')
depends=('libv4l' 'fftw' 'jack' 'tk' 'freeglut' 'libquicktime' 'libdv'
'gsl' 'imagemagick' 'ftgl' 'libgl' 'dssi' 'git' 'lua51' 'libmpeg3' 'avifile')
makedepends=('subversion' 'git' 'swig')
conflicts=('pd' 'pdp' 'zexy' 'pd-extended')
provides=('pd' 'pd-extended' 'pd-gem' 'pdp' 'zexy')
options=('!makeflags')
source=("pd-svn::svn+https://pure-data.svn.sourceforge.net/svnroot/pure-data/trunk/"
"git://pure-data.git.sourceforge.net/gitroot/pure-data/pd-extended.git"
"git://pd-gem.git.sourceforge.net/gitroot/pd-gem/Gem")
md5sums=('SKIP'
'SKIP'
'SKIP')

prepare() {
# Put sources together
  cd $srcdir/pd-svn

  mv pd pd-original
  ln -s ../pd-extended pd

  ln -s ../Gem Gem
  cd externals
  ln -s ../../Gem Gem

  # fix 1: Nonexisting file ninjacount-help.pd in externals/ekext
  cd $srcdir/pd-svn/externals/ekext
  [[ ! -f ninjacount-help.pd ]] && touch ninjacount-help.pd

  cd $srcdir/pd-svn/abstractions/jmmmp
  [[ ! -f gui-edit-help.pd ]] && touch gui-edit-help.pd
  [[ ! -f mat-~-help.pd ]] && touch mat-~-help.pd
  [[ ! -f stoppuhr-clock-help.pd ]] && touch stoppuhr-clock-help.pd

  # fix 2: Update for tcl 8.6
  cd $srcdir/pd-svn
  sed -i "s/tcl[0-9].[0-9]/tcl8.6/g" pd-original/src/configure.in externals/loaders/tclpd/Makefile
}

build() {
  cd $srcdir/pd-svn

  unset CFLAGS
  unset LDFLAGS
  unset INCLUDES

  if [ "$CARCH" = "x86_64" ]; then
    # fix -fPIC issue in PDP
    sed -i "s/CFLAGS =/CFLAGS = -fPIC/" externals/pdp/opengl/Makefile.config
    # fix -fPIC issue in pddp
    sed -i "s/DEFINES =/DEFINES = -fPIC/" externals/miXed/Makefile.common
    # setting additional variable
    local FPIC_FLAG="-fPIC"
  else
    local FPIC_FLAG=""
  fi

  cd "$srcdir/pd-svn/packages/linux_make"

  make prefix=/usr \
    BUILDLAYOUT_DIR=$srcdir/pd-svn/packages \
    GEM_EXTRA_CXXFLAGS="$FPIC_FLAG" \
    all

}

package() {
  cd "$srcdir/pd-svn/packages/linux_make"
  make prefix=/usr BUILDLAYOUT_DIR=$srcdir/pd-svn/packages \
    DESTDIR=$pkgdir install

  cd $srcdir/pd-svn/packages/
  install -p linux_make/default.pdextended $pkgdir/usr/lib/pd-extended/
  # Gnome menu support
  install -d $pkgdir/usr/share/icons/hicolor/128x128/apps
  install -p -m0644 linux_make/pd-extended.png \
    $pkgdir/usr/share/icons/hicolor/128x128/apps/
  install -d $pkgdir/usr/share/icons/hicolor/48x48/apps
  install -p -m0644 linux_make/48x48/pd-extended.png \
    $pkgdir/usr/share/icons/hicolor/48x48/apps/pd-extended.png
  install -d $pkgdir/usr/share/applications/
  install -p linux_make/pd-extended.desktop \
    $pkgdir/usr/share/applications/
  # files for /etc
  cd "$srcdir/pd-svn"
  install -d $pkgdir/etc/bash_completion.d/
  install -p scripts/bash_completion/pd $pkgdir/etc/bash_completion.d
  # emacs mode for .pd files
  install -d $pkgdir/usr/share/emacs/site-lisp/
  install -p scripts/pd-mode.el $pkgdir/usr/share/emacs/site-lisp/
  # Pd-related scripts
  install -p scripts/pd-diff $pkgdir/usr/bin/
  install -p scripts/config-switcher.sh $pkgdir/usr/bin/

}
