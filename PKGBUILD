# Maintainer: Rasmus
pkgname=dynare
pkgver=4.4.3
pkgrel=1
pkgdesc="A software platform for handling (economic) DSGE and OLG models."
arch=('i686' 'x86_64')
url="http://www.dynare.org/"
license=('GPL')
depends=('octave' 'boost' 'blas' 'flex' 'bison' 'libmatio' 'gsl' 'lapack')

makedepends=('gcc-fortran')
optdepends=('latex: manual etc. E.g. texlive-most'
            'octave-optim: suggested extension'
	    'epstools: for some graphics export formats'
	    'pstoedit: for some graphics export formats'
	    'transfig: for some graphics export formats'
	    'gnuplot: for graph display support'
	    'emacs: for building html documentation')

source=("http://www.dynare.org/release/source/${pkgname}-${pkgver}.tar.xz")
md5sums=('890c9c2f576d5636848134f30ce5ddd0')


build() {
  cd "$srcdir/$pkgname-$pkgver"
  ## TODO: add pkgbuild for slicot
  ./configure --prefix=/usr --libdir=/usr/lib/dynare\
              --disable-matlab\
              --enable-openmp \
              --with-gsl=/usr/include/gls\
              --with-mathio=/usr/include \
              --enable-org-export
  make all
  make info
  command -v emacs > /dev/null 2>&1 && make -k html || echo "Emacs required for some html doc"
  command -v pdflatex > /dev/null 2>&1 && make -k PDFLATEX="pdflatex -interaction batchmode" pdf || echo "texlive depencies not met"
}

# check() {
#   ## You may run this to test if you have all the functionalities you need
#   ## See also the optdepends array

#   cd "$srcdir/$pkgname-$pkgver"
#   make -k check ## seems to be a bug now
# }

package() {
  mkdir -p "$pkgdir/"usr/lib/dynare/{matlab,mex/octave}

  cd "$srcdir/$pkgname-$pkgver"
  make DESTDIR="$pkgdir" install
  for i in "$srcdir"/"$pkgname-$pkgver"/mex/build/octave/*/*.mex; do
    install -m 644 -D "$i" "$pkgdir/usr/lib/dynare/mex/octave/";
  done

  cp -a "$srcdir/$pkgname-$pkgver/matlab/" "$pkgdir/usr/lib/dynare/"
  cp -a "$srcdir/$pkgname-$pkgver/preprocessor" "$pkgdir/usr/lib/dynare/matlab/"

  rm "$pkgdir/usr/lib/dynare/matlab/dynare_m"
  ln -s /usr/lib/dynare/matlab/preprocessor/dynare_m  "$pkgdir"/usr/lib/dynare/matlab/dynare_m

  ## Install info and man
  make DESTDIR="$pkgdir" install-man # seems to do nothing
  make DESTDIR="$pkgdir" install-info || echo "Making info documentation failed"
  make DESTDIR="$pkgdir" install-html  || echo "Making html documentation failed"
  make DESTDIR="$pkgdir" install-pdf  || echo "Making LaTeX-PDF documentation failed"

  ## install Emacs support
  install -D -m 644 "$srcdir/$pkgname-$pkgver/scripts/dynare.el" "$pkgdir/usr/share/emacs/site-lisp/dynare.el"

  install -D -m 644 "$srcdir/$pkgname-$pkgver/doc/internals/dynare-internals.org" "$pkgdir/usr/share/doc/dynare/dynare-internals.org"
}
