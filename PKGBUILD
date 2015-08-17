# Maintainer: Jon Gjengset (Jonhoo) <jon@tsp.io>
# Contributor: Andrew Dorney <andrewd18 _at_ gmail.com>
# Upstream: https://github.com/fabianschuiki/OpenSkyscraper/tree/master/package/arch-linux

pkgname=openskyscraper-git
pkgver=263.0e88858
pkgrel=1
epoch=2
pkgdesc="Tower simulation game inspired by SimTower; based on SDL, OpenGL and OpenAL."
arch=('i686' 'x86_64')
url="https://github.com/fabianschuiki/OpenSkyscraper"
license=('GPL2')
depends=('sfml1.6' 'librocket-git' 'libmspack')
# tinyxml2 and astar-search are directly included
# librocket (non -git) does not currently build correctly
# sfml1.6: https://github.com/fabianschuiki/OpenSkyscraper/issues/51
makedepends=('cmake' 'git')
conflicts=('expac')
source=(
  'sfml1.6.patch'
  'git://github.com/fabianschuiki/OpenSkyscraper.git'
  'git+https://github.com/leethomason/tinyxml2.git'
  'git+https://github.com/hoshi10/astar-algorithm-cpp.git'
)
sha256sums=('558efa80b77d148d6888d1266a50d4c5ba26cf228880f21c983d1d22a5b99fd5'
            'SKIP'
            'SKIP'
            'SKIP')

pkgver() {
  cd "${srcdir}/OpenSkyscraper"
  printf "%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  msg "You need a full copy of this game in order to install it"
  msg "Give the path to SIMTOWER.EXE or SIMTOWER.EX_ (compressed) below"
  msg "Searching for \"SIMTOWER.EX?\" in dir: $(readlink -f ${startdir})"
  pkgpath=("${startdir}/"SIMTOWER.EX*)

  if [[ ! ( -f "${pkgpath}" ) ]]; then
    error "Game file not found, please type path manually:"
    read pkgpath
    if [[ ! ( "${pkgpath:0:1}" == "/" ) ]]; then
      pkgpath="${startdir}/${pkgpath}"
    fi
    if [[ ! ( -f "${pkgpath}" ) ]] ; then
      error "Unable to locate game file." && return 1
    fi
  fi

  msg2 "Game binary found, continuing..."
  ln -sf "${pkgpath}" "${srcdir}/"

  cd "${srcdir}/OpenSkyscraper"

  msg2 "Linking in submodules"
  git submodule init
  git config "submodule.thirdparty/tinyxml2.url" "${srcdir}/tinyxml2"
  git config "submodule.thirdparty/astar-search.url" "${srcdir}/astar-algorithm-cpp"
  git submodule update

  msg2 "Fixing path to SFML 1.6"
  patch -Np1 < ../sfml1.6.patch
}

build() {
  cd "${srcdir}/OpenSkyscraper"
  SFMLDIR=/usr/include/sfml-1.6 cmake -DCMAKE_BUILD_TYPE=Release .
  make
}

package() {
  cd "${srcdir}"
  install -d -m 755 "${pkgdir}/usr/share/OpenSkyscraper"
  cp -Lfpr "OpenSkyscraper/data/"* "${pkgdir}/usr/share/OpenSkyscraper/"
  install -m 644 SIMTOWER.EX* "${pkgdir}/usr/share/OpenSkyscraper/"
  install -D -m 755 "OpenSkyscraper/OpenSkyscraper" "${pkgdir}/usr/bin/openskyscraper"
}
