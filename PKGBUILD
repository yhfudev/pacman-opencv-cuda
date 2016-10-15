# Maintainer: yhfudev <yhfudev gmail>
# Contributor: Ray Rashif <schiv@archlinux.org>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>

pkgbase=opencv-cuda
_pkgbase=opencv
pkgname=('opencv-cuda' 'opencv-cuda-samples')
pkgver=3.1.0
pkgrel=3
pkgdesc="Open Source Computer Vision Library"
arch=('i686' 'x86_64')
license=('BSD')
url="http://opencv.org/"
depends=('intel-tbb' 'openexr' 'xine-lib' 'libdc1394' 'gtkglext')
makedepends=('cmake' 'python-numpy' 'python2-numpy' 'mesa' 'eigen' 'cuda')
conflicts=('opencv' 'opencv-samples')
provides=('opencv' 'opencv-samples')
optdepends=('opencv-samples'
            'eigen'
            'libcl: For coding with OpenCL'
            'python-numpy: Python 3 interface'
            'python2-numpy: Python 2 interface')
source=("${_pkgbase}-${pkgver}.tar.gz::https://github.com/Itseez/opencv/archive/$pkgver.tar.gz"
        "opencv_contrib-${pkgver}.tar.gz::https://github.com/Itseez/opencv_contrib/archive/$pkgver.tar.gz"
        '5852.patch'
        'opencv-3.1.0-vtk-cmake.patch'
    )
md5sums=('70e1dd07f0aa06606f1bc0e3fa15abd3'
         'a822839ad3ab79ff837c16785ea9dd10'
         '5bd9cd736b171c15cedee3a32a0c47ff'
         'fc8237ef0558515ad98d0badef68338a'
    )

_cmakeopts=('-D WITH_OPENCL=ON'
            '-D WITH_OPENGL=ON'
            '-D WITH_TBB=ON'
            '-D WITH_XINE=ON'
            '-D WITH_GSTREAMER=OFF'
            '-D BUILD_WITH_DEBUG_INFO=OFF'
            '-D BUILD_TESTS=OFF'
            '-D BUILD_PERF_TESTS=OFF'
            '-D BUILD_EXAMPLES=ON'
            '-D INSTALL_C_EXAMPLES=ON'
            '-D INSTALL_PYTHON_EXAMPLES=ON'
            '-D CMAKE_BUILD_TYPE=Release'
            '-D CMAKE_INSTALL_PREFIX=/usr'
            '-D CMAKE_SKIP_RPATH=ON'
            #'-D WITH_IPP=ON'
            #'-D INSTALL_CREATE_DISTRIB=ON'
            '-D WITH_CUDA=ON'
            '-D CUDA_TOOLKIT_ROOT_DIR=/opt/cuda/'
            '-D CUDA_GENERATION=Auto'
            '-D CUDA_ARCH_BIN=1.1'
            '-D CUDA_ARCH_PTX=1.1'
            '-D WITH_CUBLAS=ON'
            '-D WITH_NVCUVID=ON'
            '-D CUDA_FAST_MATH=ON'
            )

# SSE only available from Pentium 3 onwards (i686 is way older)
[[ "$CARCH" = 'i686' ]] && \
  _cmakeopts+=('-D ENABLE_SSE=OFF'
               '-D ENABLE_SSE2=OFF'
               '-D ENABLE_SSE3=OFF')

prepare() {
  cd "$srcdir/${_pkgbase}-$pkgver"
  patch -p1 -i "$srcdir/5852.patch"
  patch -p1 -i "$srcdir/opencv-3.1.0-vtk-cmake.patch"
}

build() {
  cd "$srcdir/${_pkgbase}-$pkgver"

  cmake ${_cmakeopts[@]} \
    -DOPENCV_EXTRA_MODULES_PATH="$srcdir/opencv_contrib-$pkgver/modules" \
    .

  make
}

package_opencv-cuda() {
  options=('staticlibs')

  cd "$srcdir/${_pkgbase}-${pkgver}"

  make DESTDIR="$pkgdir" install

  # install license file
  install -Dm644 "$srcdir/${_pkgbase}-$pkgver/LICENSE" \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE"

  cd "$pkgdir/usr/share"

  # separate samples package; also be -R friendly
  if [[ -d OpenCV/samples ]]; then
    mv OpenCV/samples "$srcdir/${_pkgbase}-samples"
    mv OpenCV ${_pkgbase} # otherwise folder naming is inconsistent
  elif [[ ! -d OpenCV ]]; then
    warning "Directory naming issue; samples package may not be built!"
  fi
}

package_opencv-cuda-samples() {
  pkgdesc+=" (samples)"
  depends=("opencv=$pkgver") # sample codes change with lib/API
  unset optdepends

  mkdir -p "$pkgdir/usr/share/opencv"
  cp -r "$srcdir/opencv-samples" "$pkgdir/usr/share/opencv/samples"

  # install license file
  install -Dm644 "$srcdir/${_pkgbase}-$pkgver/LICENSE" \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}

# vim:set ts=2 sw=2 et:
