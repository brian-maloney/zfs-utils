# Maintainer: Eli Schwartz <eschwartz@archlinux.org>
# Contributor: Iacopo Isimbaldi <isiachi@rhye.it>

# All my PKGBUILDs are managed at https://github.com/eli-schwartz/pkgbuilds

pkgname=zfs-utils
pkgver=0.8.4
pkgrel=1
pkgdesc="Userspace utilities for the Zettabyte File System."
arch=("i686" "x86_64")
url="https://zfsonlinux.org/"
license=('CDDL')
optdepends=('python: for arcstat/arc_summary/dbufstat')
source=("https://github.com/zfsonlinux/zfs/releases/download/zfs-${pkgver}/zfs-${pkgver}.tar.gz"{,.asc}
        "zfs.initcpio.install"
        "zfs.initcpio.hook")
sha256sums=('2b988f5777976f09d08083f6bebf6e67219c4c4c183c1f33033fb7e5e5eacafb'
            'SKIP'
            'da1cdc045d144d2109ec7b5d97c53a69823759d8ecff410e47c3a66b69e6518d'
            '9c20256093997f7cfa9e7eb5d85d4a712d528a6ff19ef35b83ad03fb1ceae3bc')
b2sums=('776bcd6dfab8825c07d315085e288b29bf543d6957325d5d566b7b78c04505dde9bd25eb6684cb4a1b6a657de8a4e1290d04d2b9079d26d6b834a70f1ec3b569'
        'SKIP'
        '570e995bba07ea0fb424dff191180b8017b6469501964dc0b70fd51e338a4dad260f87cc313489866cbfd1583e4aac2522cf7309c067cc5314eb83c37fe14ff3'
        'e14366cbf680e3337d3d478fe759a09be224c963cc5207bee991805312afc49a49e6691f11e5b8bbe8dde60e8d855bd96e7f4f48f24a4c6d4a8c1bab7fc2bba0')
validpgpkeys=('4F3BA9AB6D1F8D683DC2DFB56AD860EED4598027'  # Tony Hutter (GPG key for signing ZFS releases) <hutter2@llnl.gov>
              'C33DF142657ED1F7C328A2960AB9E991C6AF658B') # Brian Behlendorf <behlendorf1@llnl.gov>

prepare() {
    cd "${srcdir}"/zfs-${pkgver}

    # pyzfs is not built, but build system tries to check for python anyway
    ln -sf /bin/true python3-fake
}

build() {
    cd "${srcdir}"/zfs-${pkgver}

    ./configure --prefix=/usr \
                --sysconfdir=/etc \
                --sbindir=/usr/bin \
                --with-mounthelperdir=/usr/bin \
                --with-udevdir=/usr/lib/udev \
                --libexecdir=/usr/lib/zfs \
                --with-python="$PWD/python3-fake" \
                --enable-pyzfs=no \
                --enable-systemd \
                --with-config=user
    make
}

package() {
    cd "${srcdir}"/zfs-${pkgver}

    make DESTDIR="${pkgdir}" install
    install -D -m644 contrib/bash_completion.d/zfs "${pkgdir}"/usr/share/bash-completion/completions/zfs

    # Remove uneeded files
    rm -r "${pkgdir}"/etc/init.d
    rm -r "${pkgdir}"/etc/sudoers.d #???
    # We're experimenting with dracut in [extra], so start installing this.
    #rm -r "${pkgdir}"/usr/lib/dracut
    rm -r "${pkgdir}"/usr/lib/modules-load.d
    rm -r "${pkgdir}"/usr/share/initramfs-tools
    rm -r "${pkgdir}"/usr/share/zfs

    install -D -m644 "${srcdir}"/zfs.initcpio.hook "${pkgdir}"/usr/lib/initcpio/hooks/zfs
    install -D -m644 "${srcdir}"/zfs.initcpio.install "${pkgdir}"/usr/lib/initcpio/install/zfs
}
