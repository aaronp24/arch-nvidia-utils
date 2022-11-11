# Maintainer: Sven-Hendrik Haase <svenstaro@gmail.com>
# Maintainer: Thomas Baechler <thomas@archlinux.org>
# Contributor: James Rayner <iphitus@gmail.com>
# Contributor: Aaron Plattner <aplattner@nvidia.com>

pkgbase=nvidia-utils
pkgname=('nvidia-utils'
         'opencl-nvidia'
         'nvidia-dkms'
         'lib32-nvidia-utils'
         'lib32-opencl-nvidia')
pkgver=525.53
pkgrel=1
arch=('x86_64')
url="http://www.nvidia.com/"
license=('custom')
options=('!strip')
source=("http://download.nvidia.com/XFree86/Linux-x86_64/${pkgver}/NVIDIA-Linux-x86_64-${pkgver}.run"
        'nvidia-utils.sysusers'
        'nvidia.rules')
sha256sums=('74bb0971f04f1dddd3c4641c891706fb96e8de52e22f6079e50de76d3a51687f'
            'd8d1caa5d72c71c6430c2a0d9ce1a674787e9272ccce28b9d5898ca24e60a167'
            '4fbfd461f939f18786e79f8dba5fdb48be9f00f2ff4b1bb2f184dbce42dd6fc3')

_pkg="NVIDIA-Linux-x86_64-${pkgver}"

prepare() {
    sh "${_pkg}.run" --extract-only
    cd "${_pkg}"
    bsdtar -xf nvidia-persistenced-init.tar.bz2

    cd kernel

    sed -i "s/__VERSION_STRING/${pkgver}/" dkms.conf
    sed -i 's/__JOBS/`nproc`/' dkms.conf
    sed -i 's/__DKMS_MODULES//' dkms.conf
    sed -i '$iBUILT_MODULE_NAME[0]="nvidia"\
DEST_MODULE_LOCATION[0]="/kernel/drivers/video"\
BUILT_MODULE_NAME[1]="nvidia-uvm"\
DEST_MODULE_LOCATION[1]="/kernel/drivers/video"\
BUILT_MODULE_NAME[2]="nvidia-modeset"\
DEST_MODULE_LOCATION[2]="/kernel/drivers/video"\
BUILT_MODULE_NAME[3]="nvidia-drm"\
DEST_MODULE_LOCATION[3]="/kernel/drivers/video"\
BUILT_MODULE_NAME[4]="nvidia-peermem"\
DEST_MODULE_LOCATION[4]="/kernel/drivers/video"' dkms.conf

    # Gift for linux-rt guys
    sed -i 's/NV_EXCLUDE_BUILD_MODULES/IGNORE_PREEMPT_RT_PRESENCE=1 NV_EXCLUDE_BUILD_MODULES/' dkms.conf
}

process_manifest () {
    declare -A type_handlers=(
        # opencl-nvidia
        ["CUDA_ICD"]="opencl-nvidia install_opencl_vendor"
        ["OPENCL_LIB"]="opencl-nvidia install_lib"
        ["OPENCL_LIB_SYMLINK"]="opencl-nvidia symlink_lib_with_path"

        # nvidia-utils
        ["APPLICATION_PROFILE"]="nvidia-utils install_app_profile"
        ["CUDA_LIB"]="nvidia-utils install_lib_with_path"
        ["CUDA_SYMLINK"]="nvidia-utils symlink_lib_with_path"
        ["DOCUMENTATION"]="nvidia-utils install_doc"
        ["ENCODEAPI_LIB"]="nvidia-utils install_lib"
        ["ENCODEAPI_LIB_SYMLINK"]="nvidia-utils symlink_lib"
        ["GBM_BACKEND_LIB_SYMLINK"]="nvidia-utils symlink_gbm"
        ["GLVND_EGL_ICD_JSON"]="nvidia-utils install_egl_json"
        ["GLX_MODULE_SHARED_LIB"]="nvidia-utils install_glx_module"
        ["GLX_MODULE_SYMLINK"]="nvidia-utils symlink_glx_module"
        ["MANPAGE"]="nvidia-utils install_man"
        ["NVCUVID_LIB"]="nvidia-utils install_lib"
        ["NVCUVID_LIB_SYMLINK"]="nvidia-utils symlink_lib"
        ["NVIDIA_MODPROBE_MANPAGE"]="nvidia-utils install_man"
        ["NVIDIA_MODPROBE"]="nvidia-utils install_bin"
        ["NVIFR_LIB"]="nvidia-utils install_lib"
        ["NVIFR_LIB_SYMLINK"]="nvidia-utils symlink_lib"
        ["OPENGL_LIB"]="nvidia-utils install_lib"
        ["OPENGL_SYMLINK"]="nvidia-utils symlink_lib"
        ["SYSTEMD_SLEEP_SCRIPT"]="nvidia-utils install_lib"
        ["SYSTEMD_UNIT"]="nvidia-utils install_lib"
        ["SYSTEMD_UNIT_SYMLINK"]="nvidia-utils symlink_systemd_unit"
        ["TLS_LIB"]="nvidia-utils install_lib"
        ["UTILITY_BINARY"]="nvidia-utils install_bin"
        ["UTILITY_LIB"]="nvidia-utils install_lib"
        ["UTILITY_LIB_SYMLINK"]="nvidia-utils symlink_lib"
        ["VDPAU_LIB"]="nvidia-utils install_lib_with_path"
        ["VDPAU_SYMLINK"]="nvidia-utils symlink_lib_with_path"
        ["VULKAN_ICD_JSON"]="nvidia-utils install_vulkan_json"
        ["WINE_LIB"]="nvidia-utils install_wine_lib"
        ["XMODULE_SHARED_LIB"]="nvidia-utils install_x_driver"
        ["XORG_OUTPUTCLASS_CONFIG"]="nvidia-utils install_x_config"

        # lib32-opencl-nvidia
        ["OPENCL_LIB_32"]="lib32-opencl-nvidia install_lib32"
        ["OPENCL_LIB_SYMLINK_32"]="lib32-opencl-nvidia symlink_lib32_with_path"

        # lib32-nvidia-utils
        ["CUDA_LIB_32"]="lib32-nvidia-utils install_lib32_with_path"
        ["CUDA_SYMLINK_32"]="lib32-nvidia-utils symlink_lib32_with_path"
        ["ENCODEAPI_LIB_32"]="lib32-nvidia-utils install_lib32"
        ["ENCODEAPI_LIB_SYMLINK_32"]="lib32-nvidia-utils symlink_lib32"
        ["NVCUVID_LIB_32"]="lib32-nvidia-utils install_lib32"
        ["NVCUVID_LIB_SYMLINK_32"]="lib32-nvidia-utils symlink_lib32"
        ["OPENGL_LIB_32"]="lib32-nvidia-utils install_lib32"
        ["OPENGL_SYMLINK_32"]="lib32-nvidia-utils symlink_lib32"
        ["TLS_LIB_32"]="lib32-nvidia-utils install_lib32"
        ["UTILITY_LIB_32"]="lib32-nvidia-utils install_lib32"
        ["UTILITY_LIB_SYMLINK_32"]="lib32-nvidia-utils symlink_lib32"
        ["VDPAU_LIB_32"]="lib32-nvidia-utils install_lib32_with_path"
        ["VDPAU_SYMLINK_32"]="lib32-nvidia-utils symlink_lib32_with_path"

        # Ignored entries
        ["DKMS_CONF"]="ignored"                 # dkms isn't needed with Arch's version-locked packages
        ["DOT_DESKTOP"]="ignored"               # Use the separate Arch nvidia-settings package.
        ["EGL_CLIENT_LIB"]="ignored"            # provided by libglvnd
        ["EGL_CLIENT_SYMLINK"]="ignored"        # provided by libglvnd
        ["EGL_EXTERNAL_PLATFORM_JSON"]="ignored" # provided by egl-wayland
        ["FIRMWARE"]="ignored"                  # provided by nvidia
        ["GLVND_LIB"]="ignored"                 # provided by libglvnd
        ["GLVND_SYMLINK"]="ignored"             # provided by libglvnd
        ["GLX_CLIENT_LIB"]="ignored"            # provided by libglvnd
        ["GLX_CLIENT_SYMLINK"]="ignored"        # provided by libglvnd
        ["INSTALLER_BINARY"]="ignored"          # provided by pacman :)
        ["INTERNAL_UTILITY_BINARY"]="ignored"   # glvnd install checker, not needed
        ["INTERNAL_UTILITY_DATA"]="ignored"     # glvnd install checker, not needed
        ["INTERNAL_UTILITY_LIB"]="ignoed"       # glvnd install checker, not needed
        ["KERNEL_MODULE_SRC"]="ignored"         # kernel modules are handled by the nvidia PKGBUILD
        ["LIBGL_LA"]="ignored"                  # .la files are not needed
        ["OPENCL_WRAPPER_LIB"]="ignored"        # provided by libcl
        ["OPENCL_WRAPPER_SYMLINK"]="ignored"    # provided by libcl
        ["UTILITY_BIN_SYMLINK"]="ignored"       # provided by pacman

        ["EGL_CLIENT_LIB_32"]="ignored"         # provided by lib32-libglvnd
        ["EGL_CLIENT_SYMLINK_32"]="ignored"     # provided by lib32-libglvnd
        ["GLVND_LIB_32"]="ignored"              # provided by lib32-libglvnd
        ["GLVND_SYMLINK_32"]="ignored"          # provided by lib32-libglvnd
        ["GLX_CLIENT_LIB_32"]="ignored"         # provided by lib32-libglvnd
        ["GLX_CLIENT_SYMLINK_32"]="ignored"     # provided by lib32-libglvnd
        ["INTERNAL_UTILITY_BINARY_32"]="ignored"
        ["INTERNAL_UTILITY_LIB_32"]="ignored"
        ["OPENCL_WRAPPER_LIB_32"]="ignored"
        ["OPENCL_WRAPPER_SYMLINK_32"]="ignored"
    )

    tail -n +9 .manifest | {
        while read name perm type extra1 extra2 extra3; do
            if [ ${extra1} = COMPAT32 ]; then
                type="${type}_32"
            fi
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
}

install_app_profile()   { install -D -m$2 "$1" "${pkgdir}/usr/share/nvidia/$1"; }
install_egl_json()      { install -D -m$2 "$1" "${pkgdir}/usr/share/glvnd/egl_vendor.d/$1"; }
install_egl_platform()  { install -D -m$2 "$1" "${pkgdir}/usr/share/egl/egl_external_platform.d/$1"; }
install_glx_module()    { install -D -m$2 "$1" "${pkgdir}/usr/lib/xorg/modules/extensions/$1"; }
install_lib_with_path() { install -D -m$2 "$1" "${pkgdir}/usr/lib/$5$1"; }
install_opencl_vendor() { install -D -m$2 "$1" "${pkgdir}/etc/OpenCL/vendors/$1"; }
install_vulkan_json()   { install -D -m$2 "$1" "${pkgdir}/etc/vulkan/$4$1"; }
install_wine_lib()      { install -D -m$2 "$1" "${pkgdir}/usr/lib/nvidia/wine/$1"; }
install_x_config()      { install -D -m$2 "$1" "${pkgdir}/usr/share/X11/xorg.conf.d/$1"; }
install_x_driver()      { install -D -m$2 "$1" "${pkgdir}/usr/lib/xorg/modules/$4$1"; }

install_lib32()           { install -D -m$2 "$1" "${pkgdir}/usr/lib32/${1#./32/}"; }
install_lib32_with_path() { install -D -m$2 "$1" "${pkgdir}/usr/lib32/$5${1#./32/}"; }
symlink_lib32()           {
    if [ "$1" != libGLX_indirect.so.0 ]; then
        ln -s "$5" "${pkgdir}/usr/lib32/${1#./32/}"
    fi
}
symlink_lib32_with_path() { ln -s "$6" "${pkgdir}/usr/lib32/$5${1#./32/}"; }

install_bin() {
    case "$1" in
        nvidia-settings)
            # Use the separate Arch nvidia-settings package.
            ;;
        *)
            install -D -m$2 "$1" "${pkgdir}/usr/bin/$(basename $1)"
            ;;
    esac
}

install_lib() {
    case "$1" in
        libnvidia-gtk*|libnvidia-wayland-client*)
            # Use the separate Arch nvidia-settings package.
            ;;
        libnvidia-egl-wayland*)
            # Use the separate Arch egl-wayland package.
            ;;
        *)
            install -D -m$2 "$1" "${pkgdir}/usr/lib/$1"
            ;;
    esac
}

install_man() {
    case "$1" in
        nvidia-settings.1.gz|nvidia-installer.1.gz)
            # Skip manpages for utilities that are not packaged.
            ;;
        *)
            install -D -m$2 "$1" "${pkgdir}/usr/share/man/man1/$1"
            ;;
    esac
}

install_doc() {
    # Strip the historical NVIDIA_GLX-1.0 prefix off of the target path and
    # "html" off the source path.
    local src=$(basename $1)
    local target=${4#NVIDIA_GLX-1.0/}
    local perms=$2

    install -D -m${perms} "$1" "${pkgdir}/usr/share/doc/nvidia/${target}/${src}"
}

symlink_glx_module()    { ln -s "$5" "${pkgdir}/usr/lib/xorg/modules/extensions/$1"; }
symlink_lib()           {
    if [ "$1" != libGLX_indirect.so.0 ]; then
        ln -s "$5" "${pkgdir}/usr/lib/$1"
    fi
}
symlink_lib_with_path() { ln -s "$6" "${pkgdir}/usr/lib/$5$1"; }
symlink_systemd_unit()  {
    local linkdir="${pkgdir}/etc/systemd/system/$4/"
    mkdir -p "${linkdir}"
    ln -s "/usr/lib/systemd/system/$1" "${linkdir}/$1"
}
symlink_gbm()           {
    local linkdir="${pkgdir}/usr/lib/gbm/"
    mkdir -p "${linkdir}"
    ln -s "/usr/lib/$5" "${linkdir}/$1"
}

package_opencl-nvidia() {
    pkgdesc="OpenCL implementation for NVIDIA"
    depends=('zlib')
    optdepends=('opencl-headers: headers necessary for OpenCL development')
    provides=('opencl-driver')
    cd "${_pkg}"

    process_manifest
}

package_nvidia-dkms() {
    pkgdesc="NVIDIA drivers - module sources"
    depends=('dkms' "nvidia-utils=$pkgver" 'libglvnd')
    provides=('NVIDIA-MODULE' 'nvidia')
    conflicts=('NVIDIA-MODULE' 'nvidia')

    cd ${_pkg}

    install -dm 755 "${pkgdir}"/usr/src
    cp -dr --no-preserve='ownership' kernel "${pkgdir}/usr/src/nvidia-${pkgver}"

    install -Dt "${pkgdir}/usr/share/licenses/${pkgname}" -m644 "${srcdir}/${_pkg}/LICENSE"
}

package_nvidia-utils() {
    pkgdesc="NVIDIA drivers utilities"
    depends=('xorg-server' 'libglvnd' 'egl-wayland>=1.1.9')
    optdepends=('nvidia-settings: configuration tool'
                'xorg-server-devel: nvidia-xconfig'
                'opencl-nvidia: OpenCL support')
    conflicts=('nvidia-libgl')
    provides=('vulkan-driver' 'opengl-driver' 'nvidia-libgl')
    replaces=('nvidia-libgl')
    install="${pkgname}.install"

    cd "${_pkg}"

    # nvidia-persistenced
    install -Dm644 nvidia-persistenced-init/systemd/nvidia-persistenced.service.template "${pkgdir}/usr/lib/systemd/system/nvidia-persistenced.service"
    sed -i 's/__USER__/nvidia-persistenced/' "${pkgdir}/usr/lib/systemd/system/nvidia-persistenced.service"

    process_manifest

    install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/nvidia-utils/LICENSE"
    ln -s nvidia "${pkgdir}/usr/share/doc/nvidia-utils"

    install -Dm644 "${srcdir}/nvidia-utils.sysusers" "${pkgdir}/usr/lib/sysusers.d/$pkgname.conf"

    install -Dm644 "${srcdir}/nvidia.rules" "$pkgdir"/usr/lib/udev/rules.d/60-nvidia.rules

    echo "blacklist nouveau" | install -Dm644 /dev/stdin "${pkgdir}/usr/lib/modprobe.d/${pkgname}.conf"
    echo "nvidia-uvm" | install -Dm644 /dev/stdin "${pkgdir}/usr/lib/modules-load.d/${pkgname}.conf"
}

package_lib32-nvidia-utils() {
    pkgdesc="NVIDIA drivers utilities (32-bit)"
    depends=('lib32-zlib' 'lib32-gcc-libs' 'lib32-libglvnd' 'nvidia-utils')
    optdepends=('nvidia-settings: configuration tool'
                'xorg-server-devel: nvidia-xconfig'
                'lib32-opencl-nvidia: OpenCL support')
    conflicts=('lib32-nvidia-libgl')
    provides=('lib32-vulkan-driver' 'lib32-opengl-driver' 'lib32-nvidia-libgl')
    replaces=('lib32-nvidia-libgl')
    cd "${_pkg}"

    process_manifest
}

package_lib32-opencl-nvidia() {
    pkgdesc="OpenCL implemention for NVIDIA (32-bit)"
    depends=('lib32-zlib' 'lib32-gcc-libs')
    optdepends=('opencl-headers: headers necessary for OpenCL development')
    provides=('lib32-opencl-driver')
    cd "${_pkg}"

    process_manifest
}
