# Maintainer: Gunther Schulz < mail at guntherschulz.de >
# Contributor: Lantald < lantald at gmx.com
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: dibblethewrecker dibblethewrecker.at.jiwe.dot.org
# Contributor: Gerardo Exequiel Pozzi <vmlinuz386@yahoo.com.ar>
# Contributor: Eric Forgeot < http://esclinux.tk >
# Contributor: mutantmonkey <aur@mutantmonkey.in>

pkgname=qgis-git
pkgver=20130505
pkgrel=2
pkgdesc='Quantum GIS is a Geographic Information System (GIS) that supports vector, raster & database formats'
url='http://qgis.org/'
license=('GPL')
arch=('i686' 'x86_64')
# update to http://www.qgis.org/wiki/Building_QGIS_from_Source#Overview
depends=('libmysqlclient' 'postgresql-libs' 'sqlite3' 'jasper' 'curl' 'qt4' 'python2'
	'python2-pyqt' 'python2-qscintilla' 'giflib' 'cfitsio' 'qwt' 'gdal' 'flex' 'bison'
        'libspatialite' 'spatialindex' python2-psycopg2 )
makedepends=('git' 'cmake'
	    #'grass7-svn'
	    #'grass'
	    # uncomment above depending on if you are using grass stable or the svn package form AUR
	    'gsl' 'postgis' 'netcdf' 'fcgi' 'python2-sip' 'txt2tags')
optdepends=('postgis: postgis support and SPIT plugin'
            'fcgi: qgis mapserver'
            'python2-sip: python-support'
            'grass: grass plugin'
            'gsl: georeferencer  ')
provides=(${pkgname%-git})
conflicts=(${pkgname%-git})
#options=('!makeflags')

_gitroot=git://github.com/qgis/Quantum-GIS.git
_gitname=${pkgname%-git}
build() {
    msg "Connecting to GIT server...."

    if [ -d $_gitname ] ; then
        # pull specific branch only
        (cd $_gitname && git pull origin master)
        msg "The local files are updated."
    else
	# clone only the latest revision, saves MANY MB of download
       git clone --depth=1 $_gitroot $_gitname
    fi

    msg "Applying source fixes..."

    # python2 fix
    sed -r -i 's%PYUIC4_PROGRAM\spyuic4%PYUIC4_PROGRAM python2-pyuic4%' "${srcdir}/$_gitname/cmake/PyQt4Macros.cmake"

    # set lib dir for build
    export LD_LIBRARY_PATH="${srcdir}/$_gitname-build/output/lib"

    # cmake supports out of tree builds
    # there is no need to copy/clone - just make a new dir and specify
    # src dir
    rm -rf $_gitname-build && mkdir $_gitname-build
    cd $_gitname-build

    cmake ../$_gitname \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_SKIP_RPATH=ON \
      -DCMAKE_INSTALL_PREFIX=/usr \
      -DQGIS_MANUAL_SUBDIR=share/man \
	  -DQT_QMAKE_EXECUTABLE=/usr/bin/qmake-qt4 \
      -DWITH_PYSPATIALITE=ON \
      -DPYTHON_LIBRARY=/usr/lib/libpython2.7.so \
      -DPYTHON_EXECUTABLE=/usr/bin/python2 \
      -DPYTHON_SITE_PACKAGES_DIR=/usr/lib/python2.7/site-packages \
      -DPYTHON_INCLUDE_PATH=/usr/include/python2.7/ \
      #-DGRASS_PREFIX=/opt/grass7-svn \
	  #-DGRASS_PREFIX=/opt/grass \
	  # uncomment above depending on if you are using grass stable or the svn package form AUR
    make
}

package() {

    cd $_gitname-build

    make DESTDIR="$pkgdir" install

    #removing reference to $srcdir left by txt2tags
    sed -r -i 's%/.*/doc/INSTALL%/usr/share/qgis/doc/INSTALL%' "${pkgdir}/usr/share/qgis/doc/INSTALL.html"
    sed -r -i 's%/.*/doc/CODING%/usr/share/qgis/doc/CODING%' "${pkgdir}/usr/share/qgis/doc/CODING.html"

    #sextante python2 fix
    find ${pkgdir}/usr/share/qgis/python/plugins/sextante/otb/helper/ -name \*.py -exec sed -r -i 's%#!/usr/bin/python%#!/usr/bin/python2$%' {} \;

    # create a more user-friendly application name link
    ln -s /usr/bin/qgis "$pkgdir"/usr/bin/quantum-gis
    #mv /usr/bin/qgis /usr/bin/qgis-git
    #ln -s /usr/bin/qgis-git "$pkgdir"/usr/bin/quantum-gis-git
    #mv /usr/bin/qgis_bench

    # install some freedesktop.org compatibility
    install -D -m644 "$srcdir"/$_gitname/debian/qgis.desktop \
                        "$pkgdir"/usr/share/applications/qgis.desktop

    # fix python crunchbang in grass scripts
    #sed '1s_/usr/bin/env python_/usr/bin/env python2_' \
    #    -i "$pkgdir"/usr/share/qgis/grass/scripts/*.py
}
