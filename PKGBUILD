# $Id: PKGBUILD 239541 2015-05-18 23:34:43Z svenstaro $
# Maintainer: Sven-Hendrik Haase <sh@lutzhaase.com>
# Maintainer: Thomas Baechler <thomas@archlinux.org>
# Contributor: James Rayner <iphitus@gmail.com>
# Contributor: Aaron Plattner <aplattner@nvidia.com>
pkgbase=nvidia-utils
pkgname=('nvidia-utils' 'opencl-nvidia')
pkgver=415.18
pkgrel=1
arch=('x86_64')
url="http://www.nvidia.com/"
license=('custom')
options=('!strip')
source=("http://us.download.nvidia.com/XFree86/Linux-x86_64/${pkgver}/NVIDIA-Linux-x86_64-${pkgver}-no-compat32.run")
sha256sums=('c90af3b6cc1f524799c75da99734f37dceeed2267afc3278a293367d21edc795')

_pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

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

        # nvidia-utils
        ["APPLICATION_PROFILE"]="nvidia-utils install_app_profile"
        ["CUDA_LIB"]="nvidia-utils install_lib_with_path"
        ["CUDA_SYMLINK"]="nvidia-utils symlink_lib_with_path"
        ["DOCUMENTATION"]="nvidia-utils install_doc"
        ["ENCODEAPI_LIB"]="nvidia-utils install_lib"
        ["ENCODEAPI_LIB_SYMLINK"]="nvidia-utils symlink_lib"
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
        ["TLS_LIB"]="nvidia-utils install_lib"
        ["UTILITY_BINARY"]="nvidia-utils install_bin"
        ["UTILITY_LIB"]="nvidia-utils install_lib"
        ["UTILITY_LIB_SYMLINK"]="nvidia-utils symlink_lib"
        ["VDPAU_LIB"]="nvidia-utils install_lib_with_path"
        ["VDPAU_SYMLINK"]="nvidia-utils symlink_lib_with_path"
        ["VULKAN_ICD_JSON"]="nvidia-utils install_vulkan_json"
        ["XMODULE_SHARED_LIB"]="nvidia-utils install_x_driver"
        ["XORG_OUTPUTCLASS_CONFIG"]="nvidia-utils install_x_config"

        # Ignored entries
        ["DKMS_CONF"]="ignored"                 # dkms isn't needed with Arch's version-locked packages
        ["DOT_DESKTOP"]="ignored"               # Use the separate Arch nvidia-settings package.
        ["EGL_CLIENT_LIB"]="ignored"            # provided by libglvnd
        ["EGL_CLIENT_SYMLINK"]="ignored"        # provided by libglvnd
        ["GLVND_LIB"]="ignored"                 # provided by libglvnd
        ["GLVND_SYMLINK"]="ignored"             # provided by libglvnd
        ["GLX_CLIENT_LIB"]="ignored"            # provided by libglvnd
        ["GLX_CLIENT_SYMLINK"]="ignored"        # provided by libglvnd
        ["INSTALLER_BINARY"]="ignored"          # provided by pacman :)
        ["KERNEL_MODULE_SRC"]="ignored"         # kernel modules are handled by the nvidia PKGBUILD
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
        ["EGL_EXTERNAL_PLATFORM_JSON"]="ignored" # TODO: Install this somewhere
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
install_egl_json()      { install -D -m$2 "$1" "${pkgdir}/usr/share/glvnd/egl_vendor.d/$1"; }
install_glx_module()    { install -D -m$2 "$1" "${pkgdir}/usr/lib/nvidia/xorg/$1"; }
install_lib_with_path() { install -D -m$2 "$1" "${pkgdir}/usr/lib/$5$1"; }
install_opencl_vendor() { install -D -m$2 "$1" "${pkgdir}/etc/OpenCL/vendors/$1"; }

install_bin() {
    case "$1" in
        nvidia-settings)
            # Use the separate Arch nvidia-settings package.
            ;;
        *)
            install -D -m$2 "$1" "${pkgdir}/usr/bin/$1"
            ;;
    esac
}

install_lib() {
    case "$1" in
        libnvidia-gtk*)
            # Use the separate Arch nvidia-settings package.
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

install_vulkan_json()   {
    mkdir -p "${pkgdir}/etc/vulkan/icd.d/"
    sed -e s/__NV_VK_ICD__/libGLX_nvidia.so.${pkgver}/ "$1" > \
        "${pkgdir}/etc/vulkan/icd.d/${1/.template}"
}

install_x_config()      {
    install -D -m$2 "$1" "${pkgdir}/usr/share/X11/xorg.conf.d/$1"

    # Add a rule to pick up libglx from /usr/lib/nvidia/xorg
    sed -i -e '/ Driver/a\ \ \ \ ModulePath     "/usr/lib/nvidia/xorg"' \
        "${pkgdir}/usr/share/X11/xorg.conf.d/$1"
}

install_x_driver()      {
    case "$1" in
        libnvidia-wfb*)
            # not needed for modern X servers
            ;;
        *)
            install -D -m$2 "$1" "${pkgdir}/usr/lib/xorg/modules/$4$1";
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

symlink_glx_module()    { ln -s "$5" "${pkgdir}/usr/lib/nvidia/xorg/$1"; }
symlink_lib()           {
    if [ "$1" != libGLX_indirect.so.0 ]; then
        ln -s "$5" "${pkgdir}/usr/lib/$1"
    fi
}
symlink_lib_with_path() { ln -s "$6" "${pkgdir}/usr/lib/$5$1"; }

package_opencl-nvidia() {
    pkgdesc="OpenCL implemention for NVIDIA"
    depends=('libcl' 'zlib')
    optdepends=('opencl-headers: headers necessary for OpenCL development')
    provides=('opencl-driver')
    cd "${_pkg}"

    process_manifest
}

package_nvidia-utils() {
    pkgdesc="NVIDIA drivers utilities"
    depends=('xorg-server')
    optdepends=('xorg-server-devel: nvidia-xconfig'
                'opencl-nvidia: OpenCL support')
    install="${pkgname}.install"
    cd "${_pkg}"

    process_manifest

    install -D -m644 LICENSE "${pkgdir}/usr/share/licenses/nvidia/LICENSE"
    ln -s nvidia "${pkgdir}/usr/share/doc/nvidia-utils"
}
