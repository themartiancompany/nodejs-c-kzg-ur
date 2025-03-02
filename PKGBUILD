# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2024, 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Truocolo <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>

_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _cc="clang"
elif [[ "${_os}" == "GNU/Linux" ]]; then
  _cc="gcc"
fi
_node="nodejs"
_source="github"
_pkg="c-kzg"
_Pkg="c-kzg-4844"
pkgname="${_node}-${_pkg}"
_commit="844a22c3892fe413e85096898f842be1100ce3bc"
pkgver="2.1.3"
_blst_pkgver="v0.3.11"
_blst_commit="3dd0f804b1819e5d03fb22ca2e6fac105932043a"
pkgrel=1
_pkgdesc=(
  "A minimal implementation of the Polynomial"
  "Commitments API for EIP-4844 and EIP-7594,"
  "written in C."
)
pkgdesc="${_pkgdesc[*]}"
arch=(
  'x86_64'
  'arm'
  'aarch64'
  'armv7l'
  'mips'
  'pentium4'
  'i686'
)
_http="https://github.com"
# _ns="ethereum"
_blst_ns="supranational"
_ns="themartiancompany"
url="${_http}/${_ns}/${_Pkg}"
license=(
  "APACHE"
)
depends=(
  "${_node}"
)
makedepends=(
  'npm'
  "${_cc}"
)
if [[ "${_source}" == "github" ]]; then
  makedepends+=(
    'yarn'
  )
fi
provides=(
  "${_pkg}=${pkgver}"
)
conflicts=(
  "${_pkg}"
)
noextract=()
source=()
sha256sums=()
if [[ "${_source}" == "npm" ]]; then
  _tag_name="pkgver"
  _tag="${pkgver}"
  noextract+=(
    "${_tarball}"
  )
elif [[ "${_source}" == "github" ]]; then
  _tag_name="commit"
  _tag="${_commit}"
fi
_tarname="${_Pkg}-${_tag}"
_blst_url="${_http}/${_blst_ns}/blst"
if [[ "${_source}" == "npm" ]]; then
  _tarball="${_tarname}.tgz"
  _src="${_tarball}.tgz::${_npm}/@${_ns}/${_pkg}/-/${_tarname}.tgz"
  _sum="22d6d7007fc40fa22d565d73e008a953fa0db2ff1c5a8b2e1a2c0ea203fb6174"
elif [[ "${_source}" == "github" ]]; then
  _tarball="${_tarname}.zip"
  _src="${_tarball}::${url}/archive/${_commit}.zip"
  _sum="8dd01c27ac2e03eaa08dcd80f8be6554d000836d3065df9358231ff34b5c77ca"
  _blst_src="blst-${_blst_commit}.zip::${_blst_url}/archive/${_blst_commit}.zip"
  _blst_sum="810b9f46a48d53d504e3788276571a1e0f3118247b4b741d504a6abc2eea4591"
  source+=(
    "${_blst_src}"
  )
  sha256sums+=(
    "${_blst_sum}"
  )
fi
source+=(
  "${_src}"
)
sha256sums+=(
  "${_sum}"
)

_android_quirk() {
  local \
    _tools_bin \
    _clang \
    _compiler_dir \
    _compiler
  cd \
    "${srcdir}/${_tarname}"
  if [[ "${_os}" == "Android" ]] && \
     [[ "${_arch}" == "armv7l" ]]; then
    _clang="$( \
      command \
        -v \
        clang)"
    _tools_bin="undefined/toolchains/llvm/prebuilt/linux-x86_64/bin"
    _compiler_dir="${srcdir}/${_tarname}/${_tools_bin}"
    _compiler="${_compiler_dir}/armv7a-linux-androideabi24-clang"
    mkdir \
      -p \
      "${_compiler_dir}"
    ln \
      -s \
      "${_clang}" \
      "${_compiler}" || \
      true
  fi
  cd \
    "${srcdir}/${_tarname}"
}

_android_gyp_quirk() {
  local \
    _gyp_include \
    _ndk_check
  _gyp_include="${HOME}/.gyp/include.gypi"
  if [[ ! -e "${_gyp_include}" ]]; then
    mkdir \
      -p \
      "$(dirname \
           "${_gyp_include}")"
    echo \
      "{'variables':{'android_ndk_path':''}}" > \
      "${_gyp_include}"
  elif [[ -e "${_gyp_include}" ]]; then
    _ndk_check="$( \
      cat \
        "${_gyp_include}" | \
      grep \
        "android_ndk_path")"
    if [[ "${_ndk_check}" == "" ]]; then
      echo \
        "You should probably add" \
        "\"{'variables':{'android_ndk_path':''}}\"" \
        "to '${_gyp_include}'."
    fi
  fi
}

prepare() {
  _android_gyp_quirk
  # _android_quirk  
  if [[ "${_source}" == "github" ]]; then
    cp \
      -r \
      "${srcdir}/blst-${_blst_commit}/"* \
      "${_tarname}/blst"
    cd \
      "${_tarname}"
    sed \
      -e \
        '20,26d' \
      -i \
      "bindings/node.js/binding.gyp"
    sed \
      -e \
        '7d' \
      -i \
      "bindings/node.js/binding.gyp"
  fi
}

_blst_build() {
  local \
    _cc_opts=()
  _cc_opts+=(
    -O2
    -fno-builtin
    -fPIC
    -Wall
    -Wextra
    # -Werror
    -D__BLST_PORTABLE__
    -c
  )
  echo \
    "Building blst with" \
    "compiler '${_cc}' and" \
    "options '${_cc_opts[*]}'."
  cd \
    "${srcdir}/${_tarname}/blst"
  "${_cc}" \
    "${_cc_opts[@]}" \
    "./src/server.c"
  "${_cc}" \
    "${_cc_opts[@]}" \
    "build/assembly.S"
  echo \
    "Done."
  cd \
    "${srcdir}/${_tarname}"
}

_c_kzg_build() {
  local \
    _cflags=()
  _cflags=(
    $CFLAGS
  )
  if [[ "$CARCH" == "arm" || \
	"$CARCH" == "armv7l" || \
	"$CARCH" == 'i686' || \
	"$CARCH" == "pentium4" ]]; then
    _cflags+=(
      -Wno-constant-conversion
    )
  fi
  echo \
    "Building c-kzg."
  cd \
    "${srcdir}/${_tarname}/src"
  CFLAGS="${_cflags[*]}" \
  VERBOSE=1 \
  make
  cd \
    "${srcdir}/${_tarname}"
}

_bindings_nodejs_deps_setup() {
  mkdir \
    -p \
    "deps/blst/src"
  cp \
    "${srcdir}/${_tarname}/blst/"{"assembly.o","libblst.a","server.o"} \
    "deps/blst/src"
  cd \
    "${srcdir}/${_tarname}/bindings/node.js/deps/blst"
  rm \
    "bindings"
  ln \
    -s \
    "${srcdir}/${_tarname}/bindings/go/blst_headers" \
    "bindings"
  cd \
    "${srcdir}/${_tarname}/bindings/node.js/deps"
  rm \
    "${_pkg}"
  ln \
    -s \
    "${srcdir}/${_tarname}/src" \
    "${_pkg}"
  cd \
    "${srcdir}/${_tarname}/bindings/node.js"
}

_bindings_nodejs_build() {
  cd \
    "${srcdir}/${_tarname}/bindings/node.js"
  _bindings_nodejs_deps_setup
  yarn \
    install
  # npm \
  #   pack
  # mv \
  #   "${_pkg}-${pkgver}.tgz" \
  #   "${srcdir}"	
}

build() {
  cd \
    "${_tarname}"
  if [[ "${_source}" == "github" ]]; then
    # build c-kzg kirsh because
    # 'blst/build.sh' script called
    # by c-kzg makefile cleans the
    # directory afterwords
    _c_kzg_build
    _blst_build
    _bindings_nodejs_build
  fi
}

package() {
  local \
    _npmdir \
    _npm_opts=() \
    _src
  _npm_opts=(
    # --user
    #   root
    -g
    --prefix
      "${pkgdir}/usr"
  )
  _npmdir="${pkgdir}/usr/lib/node_modules/"
  mkdir \
    -p \
    "${_npmdir}"
  cd \
    "${_npmdir}"
  if [[ "${_source}" == "github" ]]; then
    _src="${srcdir}/${_tarname}/bindings/node.js"
  elif [[ "${_source}" == "npm" ]]; then
    _src="${srcdir}/${_pkg}-${pkgver}.tgz"
  fi
  npm \
    install \
      "${_npm_opts[@]}" \
      "${_src}"
}

