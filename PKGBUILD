# -*- shell-script -*-
#
# Contributor: Adrian C. <anrxc..sysphere.org>

pkgname=ircd-hybrid
pkgver=8.1.13
pkgrel=1
arch=("i686" "x86_64")
pkgdesc="High performance secure IRC server"
url="http://www.ircd-hybrid.org"
license=("GPL")
depends=("zlib" "openssl")
optdepends=("ircd-hybrid-serv: IRC services designed for ircd-hybrid server"
            "anope: IRC services designed for flexibility and ease of use"
            "geoip: enable basic geolocation features in ircd-hybrid")
conflicts=("ircd")
provides=("ircd")
backup=("etc/ircd-hybrid/ircd.conf"
	"etc/conf.d/ircd-hybrid")
install="${pkgname}.install"
source=("http://prdownloads.sourceforge.net/${pkgname}/${pkgname}-${pkgver}.tgz"
        "modules-install-exec.patch"
        "database-location.patch"
        "${pkgname}.rc"
        "${pkgname}.motd"
        "${pkgname}.conf"
        "${pkgname}.confd"
        "${pkgname}.service"
        "${pkgname}.tmpfiles")
md5sums=("082e7d9623ebd9d73ee6eb2f103bf97c"
         "caad81bf8958943baf3b1bf6975e5f48"
         "a73b72841f461aacb9c1aea6cdf09fa0"
         "47633c0790a095f309209e7d98033634"
         "7b76aa88ccbe366cd095f5bd2ea68151"
         "1faf7c5dccfeebb09831a8771db6c0e7"
         "26b5e5f8fdf52bb11025973d2431153b"
         "d8381ecd7dd5ec4bb151db86527e374d"
         "e51723ab29e7a3ddc3589846815de0c9")


prepare () {
  cd "${srcdir}/${pkgname}-${pkgver}"

# User compile time patches
  patch -Np1 -i ${srcdir}/modules-install-exec.patch
  patch -Np1 -i ${srcdir}/database-location.patch
}


build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

# Configure Hybrid
  ./configure --prefix=/usr \
      --bindir=/usr/bin \
      --sbindir=/usr/bin \
      --sysconfdir=/etc/${pkgname} \
      --localstatedir=/var \
      --datarootdir=/usr/share \
      --mandir=/usr/share/man \
      --includedir=/usr/include \
      --libdir=/usr/lib \
      --enable-poll \
      --enable-openssl \
      --enable-halfops

# Build Hybrid
  make
# - contrib dropped in v8.1.1
  #make -C contrib
}


package () {
  cd "${srcdir}/${pkgname}-${pkgver}"

# Install Hybrid
  make DESTDIR="${pkgdir}" install
# - contrib dropped in v8.1.1
  #make DESTDIR="${pkgdir}" -C contrib install
# - messages dropped in v8.1.1
  #make DESTDIR="${pkgdir}" -C messages install

# Resolve conflicts with other packages
# - expect and ircd-hybrid provide two different implementations, so keep ours
  mv ${pkgdir}/usr/bin/mkpasswd ${pkgdir}/usr/bin/mkpasswd-ircd

# Install Hybrid sample configuration
  install -D -m 0660 ${srcdir}/${pkgname}.conf ${pkgdir}/etc/${pkgname}/ircd.conf
  install -D -m 0660 ${srcdir}/${pkgname}.motd ${pkgdir}/etc/${pkgname}/ircd.motd

# Install Arch Linux init scripts
  install -D -m 0755 ${srcdir}/${pkgname}.rc ${pkgdir}/etc/rc.d/${pkgname}
  install -D -m 0644 ${srcdir}/${pkgname}.confd ${pkgdir}/etc/conf.d/${pkgname}

# Install systemd service file
  install -D -m 0644 ${srcdir}/${pkgname}.service ${pkgdir}/usr/lib/systemd/system/${pkgname}.service
  install -D -m 0644 ${srcdir}/${pkgname}.tmpfiles ${pkgdir}/usr/lib/tmpfiles.d//${pkgname}.conf

# Prepare extra directories
  install -d "${pkgdir}/var/log/ircd"
  install -d "${pkgdir}/var/lib/ircd"
  install -d "${pkgdir}/var/run/ircd"
  install -d -m 0750 "${pkgdir}/etc/${pkgname}/ssl"
}
