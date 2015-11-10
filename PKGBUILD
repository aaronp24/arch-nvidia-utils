# $Id: PKGBUILD 239541 2015-05-18 23:34:43Z svenstaro $
# Maintainer: Sven-Hendrik Haase <sh@lutzhaase.com>
# Maintainer: Thomas Baechler <thomas@archlinux.org>
# Contributor: James Rayner <iphitus@gmail.com>
# Contributor: Aaron Plattner <aplattner@nvidia.com>

pkgbase=nvidia-utils
pkgname=('nvidia-utils' 'nvidia-libgl' 'opencl-nvidia')
pkgver=358.09
pkgrel=1
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
license=('custom')
options=('!strip')
source=("ftp://download.nvidia.com/XFree86/Linux-x86/${pkgver}/NVIDIA-Linux-x86-${pkgver}.run"
        "ftp://download.nvidia.com/XFree86/Linux-x86_64/${pkgver}/NVIDIA-Linux-x86_64-${pkgver}-no-compat32.run")
sha1sums=('bb72fed42c6f948749af6eb18f9529d9684ab18d'
          '5ece88317a7d6362a47a08e927248ec6b33e19d1')

[[ "$CARCH" = "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    sh "${_pkg}.run" --extract-only
    cd "${_pkg}"
    bsdtar -xf nvidia-persistenced-init.tar.bz2
}

process_manifest () {
    declare -A type_handlers=(
        # opencl-nvidia
        ["CUDA_ICD"]="opencl-nvidia install_opencl_vendor"
        ["OPENCL_LIB"]="opencl-nvidia install_lib"
        ["OPENCL_LIB_SYMLINK"]="opencl-nvidia symlink_lib_with_path"

        # nvidia-libgl
        ["APPLICATION_PROFILE"]="nvidia-libgl install_app_profile"
        ["GLX_MODULE_SHARED_LIB"]="nvidia-libgl install_glx_module"
        ["GLX_MODULE_SYMLINK"]="nvidia-libgl symlink_glx_module"
        ["NVIDIA_MODPROBE_MANPAGE"]="nvidia-libgl install_man"
        ["NVIDIA_MODPROBE"]="nvidia-libgl install_bin"
        ["OPENGL_LIB"]="nvidia-libgl install_lib"
        ["OPENGL_SYMLINK"]="nvidia-libgl symlink_lib"
        ["TLS_LIB"]="nvidia-libgl install_tls"
        ["VDPAU_LIB"]="nvidia-libgl install_lib"
        ["VDPAU_SYMLINK"]="nvidia-libgl symlink_lib_with_path"
        ["XMODULE_SHARED_LIB"]="nvidia-libgl install_x_driver"
        ["XORG_OUTPUTCLASS_CONFIG"]="nvidia-libgl install_x_config"

        # nvidia-utils
        ["CUDA_LIB"]="nvidia-utils install_lib"
        ["CUDA_SYMLINK"]="nvidia-utils symlink_lib_with_path"
        ["DOCUMENTATION"]="nvidia-utils install_doc"
        ["DOT_DESKTOP"]="nvidia-utils install_dot_desktop"
        ["ENCODEAPI_LIB"]="nvidia-utils install_lib"
        ["ENCODEAPI_LIB_SYMLINK"]="nvidia-utils symlink_lib"
        ["MANPAGE"]="nvidia-utils install_man"
        ["NVCUVID_LIB"]="nvidia-utils install_lib"
        ["NVCUVID_LIB_SYMLINK"]="nvidia-utils symlink_lib"
        ["NVIFR_LIB"]="nvidia-utils install_lib"
        ["NVIFR_LIB_SYMLINK"]="nvidia-utils symlink_lib"
        ["UTILITY_BINARY"]="nvidia-utils install_bin"
        ["UTILITY_LIB"]="nvidia-utils install_lib"
        ["UTILITY_LIB_SYMLINK"]="nvidia-utils symlink_lib"

        # Ignored entries
        ["INSTALLER_BINARY"]="ignored"          # provided by pacman :)
        ["KERNEL_MODULE_SRC"]="ignored"         # kernel modules are handled by the nvidia PKGBUILD
        ["DKMS_CONF"]="ignored"                 # dkms is not used
        ["LIBGL_LA"]="ignored"                  # .la files are not needed
        ["OPENCL_WRAPPER_LIB"]="ignored"        # provided by libcl
        ["OPENCL_WRAPPER_SYMLINK"]="ignored"    # provided by libcl
        ["OPENGL_HEADER"]="ignored"             # provided by mesa
        ["UTILITY_BIN_SYMLINK"]="ignored"       # provided by pacman
        ["UVM_MODULE_SRC"]="ignored"            # kernel modules are handled by the nvidia PKGBUILD
        ["VDPAU_WRAPPER_LIB"]="ignored"         # provided by libvdpau
        ["VDPAU_WRAPPER_SYMLINK"]="ignored"     # provided by libvdpau
        ["XMODULE_NEWSYM"]="ignored"            # not needed for modern X servers
        ["XMODULE_SYMLINK"]="ignored"           # not needed for modern X servers
    )

    tail -n +9 .manifest | {
        while read name perm type extra1 extra2 extra3; do
            if [ ${type_handlers[${type}]+isset} ]; then
                local data=( ${type_handlers[${type}]} )
                local pkg=${data[0]}
                local handler=${data[1]}

                # Check if this file type is handled by the current package.
                # Otherwise, ignore it.
                if [ ${pkg} = ${pkgname} ]; then
                    eval ${handler} $name $perm $type $extra1 $extra2 $extra3
                fi
            else
                echo "Unrecognized manifest type ${type}"
                exit 1
            fi
        done
    }

    mkdir -p "${pkgdir}/usr/share/licenses"
    ln -s nvidia "${pkgdir}/usr/share/licenses/${pkgname}"
}

install_app_profile()   { install -D -m$2 "$1" "${pkgdir}/usr/share/nvidia/$1"; }
install_bin()           { install -D -m$2 "$1" "${pkgdir}/usr/bin/$1"; }
install_dot_desktop()   { install -D -m$2 "$1" "${pkgdir}/usr/share/applications/$1"; }
install_glx_module()    { install -D -m$2 "$1" "${pkgdir}/usr/lib/xorg/modules/extensions/$1"; }
install_lib()           { install -D -m$2 "$1" "${pkgdir}/usr/lib/$5$1"; }
install_man()           { install -D -m$2 "$1" "${pkgdir}/usr/share/man/man1/$1"; }
install_opencl_vendor() { install -D -m$2 "$1" "${pkgdir}/etc/OpenCL/vendors/$1"; }
install_x_config()      { install -D -m$2 "$1" "${pkgdir}/usr/share/X11/xorg.conf.d/$1"; }
install_x_driver()      { install -D -m$2 "$1" "${pkgdir}/usr/lib/xorg/modules/drivers/$1"; }

install_tls() {
    # Only "new" TLS is needed on modern systems.
    case $5 in
        CLASSIC)
            return
            ;;
        NEW)
            local libname=$(basename $1)
            install -D -m$2 "$1" "${pkgdir}/usr/lib/${libname}"
            ;;
        *)
            echo "Unrecognized TLS library type $5"
            exit 1
            ;;
    esac
}

install_doc() {
    # Strip the historical NVIDIA_GLX-1.0 prefix off of the target path and
    # "html" off the source path.
    local src=$(basename $1)
    local target=${4#NVIDIA_GLX-1.0/}
    install -D -m$2 "$1" "${pkgdir}/usr/share/doc/nvidia/${target}/${src}"
}

symlink_glx_module()    { ln -s "$5" "${pkgdir}/usr/lib/xorg/modules/extensions/$1"; }
symlink_lib()           { ln -s "$5" "${pkgdir}/usr/lib/$1"; }
symlink_lib_with_path() { ln -s "$6" "${pkgdir}/usr/lib/$5$1"; }

package_opencl-nvidia() {
    pkgdesc="OpenCL implemention for NVIDIA"
    depends=('libcl' 'zlib')
    optdepends=('opencl-headers: headers necessary for OpenCL development')
    cd "${_pkg}"

    process_manifest
}

package_nvidia-libgl() {
    pkgdesc="NVIDIA drivers libraries"
    conflicts=('libgl')
    provides=('libgl')
    optdepends=('libvdpau: VDPAU wrapper library')
    cd "${_pkg}"

    process_manifest
}

package_nvidia-utils() {
    pkgdesc="NVIDIA drivers utilities"
    depends=('xorg-server')
    optdepends=('gtk3: nvidia-settings'
                'xorg-server-devel: nvidia-xconfig'
                'opencl-nvidia: OpenCL support')
    install="${pkgname}.install"
    cd "${_pkg}"

    process_manifest

    install -D -m644 LICENSE "${pkgdir}/usr/share/licenses/nvidia/LICENSE"
    ln -s nvidia "${pkgdir}/usr/share/doc/nvidia-utils"
}
