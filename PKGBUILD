# Maintainer: Barton Cline <barton bcdesignswell com>
# Contributor: Purch <purcher@gmail.com>
# Contributor: Daniel J Griffiths <ghost1227@archlinux.us>

pkgname=synergy-plus-gui-svn
pkgver=1536
pkgrel=1
pkgdesc="Share a single mouse and keyboard between multiple computers"
url="http://synergy-foss.org"
arch=('i686' 'x86_64')
depends=('gcc-libs' 'libxtst' 'libxinerama' 'bash' 'qt4')
conflicts=('synergy' 'synergy-beta' 'synergy-beta-svn')
provides=('synergy' 'synergy-beta')
license=('GPL2')
makedepends=('libxt' 'cmake' 'python2')       # used by configure to test for libx11...
backup=('etc/synergy.conf')
source=(synergys.rc
		"synergy.desktop"
		"synergy.png"
		"DoubleClickActivateServerExternalSetting.patch")
md5sums=('8f8c01add9bf6e3ae9f37a36ca6345b6'
         '1e8d574c19994d13e6f342ed08e531bb'
         '11fdb13390908e02c6e2f19c4c09eded'
         'cb06fd8d05bf7cbae076e161505059b5')


_svntrunk="http://svn.synergy-foss.org/tags/1.4.9/"
_svnmod="synergy"

build() {
  cd $srcdir
msg "Connecting to $_svntrunk SVN server...."

  if [ -d $_svnmod/.svn ]; then
    (cd $_svnmod && svn up -r $pkgver)
    msg "SVN update done or server timeout"
  else
    svn co $_svntrunk --config-dir ./ -r $pkgver $_svnmod
    msg "SVN checkout done or server timeout"
  fi

  msg "Applying patches..."
  (cd $_svnmod && svn patch ../../DoubleClickActivateServerExternalSetting.patch)
  
  cp -r $_svnmod $_svnmod-build
  cd $_svnmod-build
  
  source /etc/profile.d/qt4.sh
  msg "Starting make..."  
  cmake  -DQT_QMAKE_EXECUTABLE=qmake-qt4 -DCMAKE_INSTALL_PREFIX=/usr .
  #cmake  -D CMAKE_INSTALL_PREFIX=/usr -D CMAKE_CXX_FLAGS='-g' .   # enable this line to compile debug symbols into the binaries
  make
  
  msg "Building GUI"
  (cd src/gui && qmake-qt4 && make)

}

package() {
msg "Packaging in $pkgdir"
msg "cd $srcdir/$_svnmod-build/$_svnmod"
  cd $srcdir/$_svnmod-build
  
  # install binary
  install -d "$pkgdir/usr/bin/"
  install -Dm755 bin/synergyc "$pkgdir/usr/bin/"
  install -Dm755 bin/synergys "$pkgdir/usr/bin/"
  # install the gui
  install -Dm755 bin/synergy "$pkgdir/usr/bin/"
  
  
  # install desktop entries in /usr/share/applications/
  install -d "${pkgdir}/usr/share/applications"
  # originally from res/synergy.desktop (could still use that one)
  install -Dm644 ../../synergy.desktop "${pkgdir}/usr/share/applications" # true
  
  # install icons in /usr/share/pixmaps/
  install -d "${pkgdir}/usr/share/pixmaps"
  # originally from res/synergy.ico converted using Gwenview
  install -Dm644 ../../synergy.png "${pkgdir}/usr/share/pixmaps" # true

  # install rc.d script  and config
  install -d "$pkgdir/etc/rc.d"
  install -Dm644 doc/synergy.conf.example-basic "$pkgdir/etc/synergy.conf" # true
  install -Dm755 "$srcdir/synergys.rc" "$pkgdir/etc/rc.d/synergys" # true
  
#  rm -r $srcdir/$_svnmod-build
}

