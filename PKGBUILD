# Initially based on Archlinux amdvlk PKGBUILD - https://www.archlinux.org/packages/extra/x86_64/amdvlk/
### Maintainer: Laurent Carlier <lordheavym@gmail.com>
### Contributor: lod <aur@cyber-anlage.de>

# Modified for multilib, opt path and a couple tweaks by: Tk-Glitch <ti3nou at gmail dot com> 

plain '       .---.`               `.---.'
plain '    `/syhhhyso-           -osyhhhys/`'
plain '   .syNMdhNNhss/``.---.``/sshNNhdMNys.'
plain '   +sdMh.`+MNsssssssssssssssNM+`.hMds+'
plain '   :syNNdhNNhssssssssssssssshNNhdNNys:'
plain '    /ssyhhhysssssssssssssssssyhhhyss/'
plain '    .ossssssssssssssssssssssssssssso.'
plain '   :sssssssssssssssssssssssssssssssss:'
plain '  /sssssssssssssssssssssssssssssssssss/'
plain ' :sssssssssssssoosssssssoosssssssssssss:'
plain ' osssssssssssssoosssssssoossssssssssssso'
plain ' osssssssssssyyyyhhhhhhhyyyyssssssssssso'
plain ' /yyyyyyhhdmmmmNNNNNNNNNNNmmmmdhhyyyyyy/'
plain '  smmmNNNNNNNNNNNNNNNNNNNNNNNNNNNNNmmms'
plain '   /dNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNd/'
plain '    `:sdNNNNNNNNNNNNNNNNNNNNNNNNNds:`'
plain '       `-+shdNNNNNNNNNNNNNNNdhs+-`'
plain '             `.-:///////:-.`'

pkgname=amdvlk-tkg
pkgver=2023.Q1.1
pkgrel=1
pkgdesc="AMD's standalone Vulkan driver"
arch=(x86_64)
url="https://github.com/GPUOpen-Drivers"
license=('MIT')
provides=('vulkan-driver' 'lib32-vulkan-driver' 'amdvlk' 'lib32-amdvlk')
makedepends=('directx-shader-compiler' 'perl-xml-xpath' 'python' 'wayland' 'lib32-wayland' 'libxrandr' 'lib32-libxrandr' 'xorg-server-devel' 'cmake' 'ninja' 'git' 'glslang' 'ocaml-stdlib-shims')
options=('!lto')
source=("https://github.com/GPUOpen-Drivers/AMDVLK/archive/v-${pkgver}.tar.gz")
sha256sums=('5511ccf9ff052808b8305aa46acd4a807fd7d95a7520d7d4a3fd8893f7937ffa')

# Workaround for chroot
if [[ "$PATH" != *"/bin/vendor_perl"* ]];then
  export PATH="$PATH:/usr/bin/vendor_perl"
fi

prepare() {
  local nrepos path name revision
  
  nrepos=$(xpath -q -e //project AMDVLK-v-${pkgver}/default.xml | wc -l)
  
  while (($nrepos>0))
  do
    path=$(xpath -q -e //project[$nrepos]/@path AMDVLK-v-${pkgver}/default.xml | sed 's/ path="drivers\/\(.*\)"/\1/g')
    name=$(xpath -q -e //project[$nrepos]/@name AMDVLK-v-${pkgver}/default.xml | sed 's/ name="\(.*\)"/\1/g')
    revision=$(xpath -q -e //project[$nrepos]/@revision AMDVLK-v-${pkgver}/default.xml | sed 's/ revision="\(.*\)"/\1/g')
    git clone https://github.com/GPUOpen-Drivers/$name $path || true
      pushd $path
        git pull https://github.com/GPUOpen-Drivers/$name
        git checkout $revision
      popd
    (( nrepos-- ))
  done

  # Workaround to clone the llvm-dialects repo to the right place for the compiler to work
  rm -rf ${srcdir}/llpc/imported/llvm-dialects
  git clone https://github.com/GPUOpen-Drivers/llvm-dialects ${srcdir}/llpc/imported/llvm-dialects
  cd ${srcdir}/llpc/imported/llvm-dialects/include/llvm-dialects
  git checkout 0ac13b7b623fa7770952ded7f7995c573993a579

}

build() {
  # use lld and clang to fix linking error
  # https://github.com/GPUOpen-Drivers/llpc/issues/1645
  cd ${srcdir}/xgl
  cmake -H. -Bbuilds/Release64 \
    -DCXX_STANDARD=17 \
    -DCMAKE_C_COMPILER=clang \
    -DCMAKE_CXX_COMPILER=clang++ \
    -DLLVM_USE_LINKER=lld \
    -DCMAKE_EXE_LINKER_FLAGS='-fuse-ld=lld' \
    -DCMAKE_SHARED_LINKER_FLAGS='-fuse-ld=lld' \
    -DCMAKE_BUILD_TYPE=Release \
    -DBUILD_WAYLAND_SUPPORT=On \
    -G Ninja
    
  ninja -C builds/Release64
  # only for compiling glsl with the standalone compiler (amdllpc)
  #ninja -C builds/Release64 spvgen

  export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"

  # use lld and clang to fix linking error
  # https://github.com/GPUOpen-Drivers/llpc/issues/1645
  cd ${srcdir}/xgl
  cmake -H. -Bbuilds/Release \
    -DCXX_STANDARD=17 \
    -DCMAKE_C_COMPILER=clang \
    -DCMAKE_CXX_COMPILER=clang++ \
    -DLLVM_USE_LINKER=lld \
    -DCMAKE_EXE_LINKER_FLAGS='-fuse-ld=lld' \
    -DCMAKE_SHARED_LINKER_FLAGS='-fuse-ld=lld' \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_C_FLAGS=-m32 \
    -DCMAKE_CXX_FLAGS=-m32 \
    -DLLVM_TARGET_ARCH:STRING=i686 \
    -DLLVM_DEFAULT_TARGET_TRIPLE="i686-pc-linux-gnu" \
    -DBUILD_WAYLAND_SUPPORT=On \
    -G Ninja
    
  ninja -C builds/Release
}

package() {
  install -m755 -d "${pkgdir}"/opt/amdvlk/lib/x86_64-linux-gnu
  install -m755 -d "${pkgdir}"/opt/amdvlk/lib/i386-linux-gnu
  install -m755 -d "${pkgdir}"/opt/amdvlk/etc/vulkan/icd.d
  install -m755 -d "${pkgdir}"/usr/share/licenses/${pkgname}

  install xgl/builds/Release64/icd/amdvlk64.so "${pkgdir}"/opt/amdvlk/lib/x86_64-linux-gnu/
  install xgl/builds/Release/icd/amdvlk32.so "${pkgdir}"/opt/amdvlk/lib/i386-linux-gnu/

  #install xgl/builds/Release64/spvgen/spvgen.so ${pkgdir}/opt/amdvlk/lib/x86_64-linux-gnu/

  install xgl/builds/Release64/icd/amd_icd64.json "${pkgdir}"/opt/amdvlk/etc/vulkan/icd.d/
  install xgl/builds/Release/icd/amd_icd32.json "${pkgdir}"/opt/amdvlk/etc/vulkan/icd.d/

  install AMDVLK-v-${pkgver}/LICENSE.txt "${pkgdir}"/usr/share/licenses/${pkgname}/
  
  sed -i "s|/usr/lib/amdvlk64.so|/opt/amdvlk/lib/x86_64-linux-gnu/amdvlk64.so|g" "${pkgdir}"/opt/amdvlk/etc/vulkan/icd.d/amd_icd64.json
  sed -i "s|/usr/lib/amdvlk32.so|/opt/amdvlk/lib/i386-linux-gnu/amdvlk32.so|g" "${pkgdir}"/opt/amdvlk/etc/vulkan/icd.d/amd_icd32.json

  msg2 "#################################################################"
  msg2 ""
  msg2 "64-bit loader in /opt/amdvlk/etc/vulkan/icd.d/amd_icd64.json"
  msg2 "32-bit loader in /opt/amdvlk/etc/vulkan/icd.d/amd_icd32.json"
  msg2 ""
  msg2 "64-bit lib in /opt/amdvlk/lib/x86_64-linux-gnu/amdvlk64.so"
  msg2 "32-bit lib in /opt/amdvlk/lib/i386-linux-gnu/amdvlk32.so"
  msg2 ""
  msg2 "changelog and copyright in /usr/share/licenses/${pkgname}"
  msg2 ""
  msg2 "#################################################################"
}

function exit_cleanup {
  # Sanitization
  rm -rf "$srcdir"
  msg2 'exit cleanup done'

  remove_deps
}

trap exit_cleanup EXIT
