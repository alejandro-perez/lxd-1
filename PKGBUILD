# Maintainer: Maikel Wever <maikelwever@gmail.com>
# Contributor: Benjamin Asbach <archlinux-aur.lxd@impl.it>

pkgname=lxd
pkgver=2.20
pkgrel=2
pkgdesc="REST API, command line tool and OpenStack integration plugin for LXC."
arch=('x86_64')
url="https://github.com/lxc/lxd"
license=('APACHE')
conflicts=('lxd-lts')
depends=('lxc' 'squashfs-tools' 'dnsmasq' 'sqlite')
makedepends=('go' 'git')
options=('!strip' '!emptydirs')
optdepends=(
    'lvm2: for lvm2 support'
    'thin-provisioning-tools: for thin provisioning support'
    'btrfs-progs: for btrfs support'
    'linux-userns: kernel with CONFIG_USER_NS enabled'
    'linux-lts-userns: LTS kernel with CONFIG_USER_NS enabled'
)
source=(
    "https://github.com/lxc/$pkgname/archive/$pkgname-$pkgver.tar.gz"
    "lxd.service"
    "dnsmasq-lxd.conf"
    "dnsmasq@lxd.service"
    "lxd.netctl"
    "dbus-dnsmasq-lxd.conf"
    "networkmanager-dnsmasq-lxd.conf"
)

md5sums=('f77a393a9b8236d49d42914abc49e33b'
         '5dde136f2fbcdf5773a011a39c82cfc6'
         'b1fd16933c1b24aaa9ccc8f5a0e6478c'
         '15ae1bc51684d611bded2839ca55a37b'
         '52c641ea0ba5477f5c1a1b857c03dda9'
         'c86b8c441ab014340186acc7799096f2'
         '427926fddb1537f7a65d0a7274106df5')

build() {
  export GOPATH="${srcdir}/go"
  go_base=github.com/lxc/lxd
  mkdir -p "${GOPATH}"
  GOPATH="${GOPATH}" go get "${go_base}" || echo "(ignoring go error)"
  cd "${GOPATH}/src/${go_base}"
  make
}

package() {
  go_bin_dir="${GOPATH}/bin"
  install=lxd.install
  mkdir -p "${pkgdir}/usr/bin"
  mkdir -p "${pkgdir}/usr/lib/lxd"
  mkdir -p "${pkgdir}/usr/share/bash-completion/completions"
  install -p -m755 "${go_bin_dir}/"* "${pkgdir}/usr/bin"

  # Package license (if available)
  for f in LICENSE COPYING LICENSE.* COPYING.*; do
    if [ -e "${go_bin_dir}/$f" ]; then
      install -Dm644 "${go_bin_dir}/$f" \
        "${pkgdir}/usr/share/licenses/${pkgname}/$f"
    fi
  done
  install -D -m644 "${srcdir}/lxd.service" \
    "${pkgdir}/usr/lib/systemd/system/lxd.service"

  # Bash completions
  install -p -m755 "${srcdir}/${pkgname}-${pkgname}-${pkgver}/config/bash/lxd-client" \
    "${pkgdir}/usr/share/bash-completion/completions/lxd"

  # Example configuration files
  mkdir -p "${pkgdir}/usr/share/lxd/"
  mkdir -p "${pkgdir}/usr/share/lxd/systemd/system/"
  mkdir -p "${pkgdir}/usr/share/lxd/netctl/"
  mkdir -p "${pkgdir}/usr/share/lxd/dbus-1/system.d/"
  mkdir -p "${pkgdir}/usr/share/lxd/NetworkManager/dnsmasq.d/"

  install -Dm644 "${srcdir}/dnsmasq-lxd.conf" \
    "${pkgdir}/usr/share/lxd/dnsmasq-lxd.conf"
  install -Dm644 "${srcdir}/dnsmasq@lxd.service" \
    "${pkgdir}/usr/share/lxd/systemd/system/dnsmasq@lxd.service"
  install -Dm644 "${srcdir}/lxd.netctl" \
    "${pkgdir}/usr/share/lxd/netctl/lxd"
  install -Dm644 "${srcdir}/dbus-dnsmasq-lxd.conf" \
    "${pkgdir}/usr/share/lxd/dbus-1/system.d/dnsmasq-lxd.conf"
  install -Dm644 "${srcdir}/networkmanager-dnsmasq-lxd.conf" \
    "${pkgdir}/usr/share/lxd/NetworkManager/dnsmasq.d/lxd.conf"
}

# vim:set ts=2 sw=2 et:
