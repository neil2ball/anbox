# Contributor: Iwan Timmer <irtimmer@gmail.com>
# Maintainer: Danct12 <danct12@disroot.org>

pkgname=anbox
pkgver=0_git20210106
pkgrel=1
arch=(x86_64 armv7h aarch64)
url="http://anbox.io/"
license=('GPL3')
depends=('lxc' 'sdl2_image' 'protobuf' 'libsystemd' 'boost-libs<1.76.0' 'dbus' 'systemd')
makedepends=('cmake' 'git' 'glm' 'lxc' 'sdl2_image' 'protobuf' 'properties-cpp' 'boost' 'python3' 'gtest')
_commit="165db5c3fccef27244a3cd8de864317a70cf3f77"
_cpu_features_commit="b5c271c53759b2b15ff91df19bd0b32f2966e275"
_sdbus_cpp_commit="3b735bf1aad65277f56e65c828a22455cbaf5245"
source=("$pkgname-$_commit.tar.gz::https://github.com/anbox/anbox/archive/$_commit.tar.gz"
	"cpu_features-$_cpu_features_commit.tar.gz::https://github.com/google/cpu_features/archive/$_cpu_features_commit.tar.gz"
	"sdbus_cpp-$_sdbus_cpp_commit.tar.gz::https://github.com/Kistler-Group/sdbus-cpp/archive/$_sdbus_cpp_commit.tar.gz"
	'inputmethod-hack.patch'
	'give-more-time-to-start.patch'
	'0001-Don-t-display-Android-apps-in-the-app-drawer.patch'
	'anbox-container-manager.service'
	'anbox-session-manager.service'
	'99-anbox.rules'
	'anbox.desktop'
	'dev-binderfs.mount'
	'binderfs.patch')

prepare() {
	cd "$srcdir/$pkgname-$_commit"

	# the bundled cpu_features is outdated and breaks build on arm.
	rm -r external/cpu_features
	cp -r $srcdir/cpu_features-*/ external/cpu_features

	rm -r external/sdbus-cpp
	cp -r $srcdir/sdbus-cpp-*/ external/sdbus-cpp

	# inputmethod hack
	patch -p1 -N < ../inputmethod-hack.patch

	patch -p1 -N < ../give-more-time-to-start.patch
	patch -p1 -N < ../0001-Don-t-display-Android-apps-in-the-app-drawer.patch
	patch -p1 -N < ../binderfs.patch

	# Don't build tests
	truncate -s 0 cmake/FindGMock.cmake
	truncate -s 0 tests/CMakeLists.txt
}

build() {
	mkdir -p "$srcdir/$pkgname-$_commit/build"
	cd "$srcdir/$pkgname-$_commit/build"

	cmake .. \
		-DCMAKE_INSTALL_PREFIX=/usr \
		-DCMAKE_INSTALL_LIBDIR=lib \
		-DBUILD_SHARED_LIBS=True \
		-DCMAKE_BUILD_TYPE=None \
		-DBUILD_TESTING=False \
		-DANBOX_VERSION=danctnix-$pkgver-r$pkgrel \
		-DWerror=OFF
	make -j1
}

package() {
  cd "$srcdir/$pkgname-$_commit"
  make -C build DESTDIR="$pkgdir" install

  install -Dm644 -t $pkgdir/usr/lib/systemd/system $srcdir/anbox-container-manager.service
  install -Dm644 -t $pkgdir/usr/lib/systemd/user $srcdir/anbox-session-manager.service
  install -Dm644 $srcdir/dev-binderfs.mount $pkgdir/usr/lib/systemd/system/dev-binderfs.mount
  install -Dm644 -t $pkgdir/usr/lib/udev/rules.d $srcdir/99-anbox.rules
  install -Dm644 -t $pkgdir/usr/share/applications $srcdir/anbox.desktop
  install -Dm644 snap/gui/icon.png $pkgdir/usr/share/pixmaps/anbox.png

  install -Dm755 scripts/anbox-bridge.sh $pkgdir/usr/share/anbox/anbox-bridge.sh
  install -Dm755 scripts/anbox-shell.sh $pkgdir/usr/share/anbox/anbox-shell.sh

  # start the container service on boot
  mkdir -p $pkgdir/usr/lib/systemd/system/multi-user.target.wants
  ln -sfv ../anbox-container-manager.service $pkgdir/usr/lib/systemd/system/multi-user.target.wants/anbox-container-manager.service
}
md5sums=('3d0176af26237da57d1bb6363eab74f8'
         '68954a1285359f2de777a4a6aa82bcc7'
         '403b77272d205aaa2bfcdceb8fa3afa3'
         '2dc0a582de4429cd28f050f58cf1f815'
         '4ccfa0d5d1188bde722077048d02e84a'
         'da64f8005b395033419f759b5f616d0f'
         '654183805a524635454632712db618ed'
         '5a4356199eb722a970274e3255250399'
         'a1bb2cb220c3a24d06138ec5824d1898'
         '587b14d7f5d9143d896e589668485bfc'
         '59f64a172dfebed502e5f82a2b220784'
	 'bde84d86f324711a00c3b234b365578b')
