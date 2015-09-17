pkgname=athame-zsh
pkgver=138.6e3c49f
pkgrel=1
pkgdesc="athame zsh"
depends=('pcre' 'libcap' 'gdbm')
arch=('i686' 'x86_64')
license=('custom')
makedepends=('pcre' 'libcap' 'gdbm')
conflicts=('zsh')
install=zsh.install

source=(
    "git://github.com/ardagnir/athame"
    "git://github.com/ardagnir/vimbed"
    "http://www.zsh.org/pub/old/zsh-5.0.8.tar.bz2"
)
md5sums=(
    'SKIP'
    'SKIP'
    'e6759e8dd7b714d624feffd0a73ba0fe'
)
backup=()

pkgver() {
    cd "$srcdir/athame"
    echo $(git rev-list --count master).$(git rev-parse --short master)
}

build() {
    cd "$srcdir"

    athamedir=$srcdir/athame
    zshdir=$srcdir/zsh-5.0.8
    zshsrcdir=$zshdir/Src

    cd "$athamedir"
    git submodule init
    git config submodule.vimbed.url "$srcdir/vimbed"
    git submodule update

    (
        cd $zshsrcdir
        patch -p1 < $athamedir/zsh.patch
    )
    cp -r $athamedir/vimbed $zshsrcdir/
    cp $athamedir/athame.* $zshsrcdir/Zle/
    cp $athamedir/athame_zsh.h $zshsrcdir/Zle/athame_intermediary.h

    (
        cd $zshdir

        sed -i 's#/usr/share/keymaps#/usr/share/kbd/keymaps#g' Completion/Unix/Command/_loadkeys
        sed -i 's#/usr/share/misc/usb.ids#/usr/share/hwdata/usb.ids#g' Completion/Linux/Command/_lsusb
        for _fpath in AIX BSD Cygwin Darwin Debian Mandriva openSUSE Redhat Solaris; do
            rm -rf Completion/$_fpath
            sed "s#\s*Completion/$_fpath/\*/\*##g" -i Src/Zle/complete.mdd
        done
        rm Completion/Linux/Command/_{pkgtool,rpmbuild}
        rm Completion/Unix/Command/_systemd

        ./configure --prefix=/usr \
            --docdir=/usr/share/doc/zsh \
            --htmldir=/usr/share/doc/zsh/html \
            --enable-etcdir=/etc/zsh \
            --enable-zshenv=/etc/zsh/zshenv \
            --enable-zlogin=/etc/zsh/zlogin \
            --enable-zlogout=/etc/zsh/zlogout \
            --enable-zprofile=/etc/zsh/zprofile \
            --enable-zshrc=/etc/zsh/zshrc \
            --enable-maildir-support \
            --with-term-lib='ncursesw' \
            --enable-multibyte \
            --enable-function-subdirs \
            --enable-fndir=/usr/share/zsh/functions \
            --enable-scriptdir=/usr/share/zsh/scripts \
            --with-tcsetpgrp \
            --enable-pcre \
            --enable-cap \
            --enable-zsh-secure-free
        make
    )
}

package() {
    cd "$srcdir/zsh-5.0.8"
    # fixing 'install.vimbed'
    mkdir -p "$pkgdir/usr/lib"
    make DESTDIR="$pkgdir/" install
    install -D -m644 "$srcdir/athame/athamerc" "$pkgdir/etc/athamerrc"
    install -D -m644 LICENCE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
