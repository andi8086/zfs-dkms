pkgname=zfs-dkms
pkgver=0.8.0
pkgrel=4
pkgdesc="Kernel modules for the Zettabyte File System."
arch=('any')
url="https://zfsonlinux.org/"
license=('CDDL')
makedepends=('git')
provides=("${pkgname%-dkms}")

source=("git+https://github.com/zfsonlinux/zfs.git#commit=d46f0deb035bd26840dc6284fc6e1bb392097c92")

sha256sums=('SKIP')

prepare() {
    cd "${srcdir}"/${pkgname%-dkms}

    patch -p1 -i ${startdir}/dkms.mkconf.patch
    patch -p1 -i ${startdir}/META.patch
    # remove unneeded sections from module build
    sed -ri "/AC_CONFIG_FILES/,/]\)/{
/AC_CONFIG_FILES/n
/]\)/n
/^\s*(module\/.*)?(${pkgname%-dkms}.release|Makefile)/!d
}" configure.ac

    autoreconf -fi

    scripts/make_gitrev.sh
}

package() {
    depends=("zfs-utils=${pkgver}" 'dkms')

    cd "${srcdir}"/${pkgname%-dkms}

    dkmsdir="${pkgdir}/usr/src/${pkgname%-dkms}-${pkgver}"
    install -d "${dkmsdir}"/{config,scripts}
    cp -a configure Makefile.in META ${pkgname%-dkms}_config.h.in ${pkgname%-dkms}.release.in include/ module/ "${dkmsdir}"/
    cp config/config.* config/missing config/*sh "${dkmsdir}"/config/
    cp scripts/enum-extract.pl scripts/dkms.postbuild "${dkmsdir}"/scripts/

    ./scripts/dkms.mkconf -n ${pkgname%-dkms} -v ${pkgver} -f "${dkmsdir}"/dkms.conf
}
