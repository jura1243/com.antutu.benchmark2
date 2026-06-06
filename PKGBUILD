# Maintainer: zhullyb <zhullyb [at] outlook dot com>

pkgname=com.antutu.benchmark
pkgver=1.0.0.591
pkgrel=4
pkgdesc="安兔兔评测 for linux
 安兔兔评测（AnTuTu）是一款跨平台，支持手机、电脑设备的专业性能评定软件。Linux 版本的安兔兔支持一键跑分，可评估 CPU/GPU/MEM/UX 性能。"
arch=("x86_64")
url="https://www.antutu.com/"
license=("custom")
depends=()
options=(!strip)
provides=('antutu')
source=("http://file.antutu.com/soft/com.antutu.benchmark_amd64.deb"
        "fix-desktop-paths.patch")
md5sums=('f114e5fe49ad569a5ce412247a72644e'
         'SKIP')

prepare(){
    cd ${srcdir}
    tar -xvf data.tar.zst -C "${srcdir}"
    
    # Apply patch to fix desktop file paths
    patch -p1 < "${srcdir}/fix-desktop-paths.patch"
}

package(){
    cd ${srcdir}

    mkdir -p ${pkgdir}/opt/antutu
    mv opt/apps/${pkgname}/files/* ${pkgdir}/opt/antutu/

    mkdir -p ${pkgdir}/usr/share/
    mv opt/apps/${pkgname}/entries/*  ${pkgdir}/usr/share/

    echo '''#!/bin/bash

# AnTuTu Benchmark Launcher Script
# Supports hybrid graphics (NVIDIA Optimus / AMD Switchable Graphics)

export LD_LIBRARY_PATH=/opt/antutu:$LD_LIBRARY_PATH
export LANG=en_US.UTF-8

# Function to detect and run on discrete GPU
run_with_dgpu() {
    # Check for NVIDIA GPU
    if command -v nvidia-smi &> /dev/null; then
        # Try primusrun first (for Bumblebee/Primus)
        if command -v primusrun &> /dev/null; then
            echo "Running on NVIDIA GPU via primusrun..."
            primusrun /opt/antutu/bin/benchmark -start "$1"
            return $?
        fi
        # Try nvidia-prime (prime-run from nvidia-prime package)
        if command -v prime-run &> /dev/null; then
            echo "Running on NVIDIA GPU via prime-run..."
            __NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia __VK_LAYER_NV_optimus=NVIDIA_only /opt/antutu/bin/benchmark -start "$1"
            return $?
        fi
        # Fallback: set environment variables manually
        echo "Running on NVIDIA GPU with manual configuration..."
        __NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia __VK_LAYER_NV_optimus=NVIDIA_only /opt/antutu/bin/benchmark -start "$1"
        return $?
    fi
    
    # Check for AMD GPU (via DRI_PRIME)
    if command -v glxinfo &> /dev/null; then
        if DRI_PRIME=1 glxinfo | grep -q "OpenGL renderer"; then
            echo "Running on AMD GPU via DRI_PRIME=1..."
            DRI_PRIME=1 /opt/antutu/bin/benchmark -start "$1"
            return $?
        fi
    fi
    
    # Default: run without discrete GPU
    /opt/antutu/bin/benchmark -start "$1"
}

# Check command line arguments
case "$1" in
    --dgpu|--discrete-gpu|-d)
        run_with_dgpu "$2"
        ;;
    --help|-h)
        echo "AnTuTu Benchmark Launcher"
        echo ""
        echo "Usage:"
        echo "  antutu [OPTIONS] [ARGS]"
        echo ""
        echo "Options:"
        echo "  -d, --dgpu, --discrete-gpu  Run on discrete GPU (NVIDIA/AMD)"
        echo "  -h, --help                  Show this help message"
        echo ""
        echo "Environment Variables:"
        echo "  ANTOTU_USE_DGPU=1           Force use of discrete GPU"
        echo ""
        echo "Examples:"
        echo "  antutu                      Run with default settings"
        echo "  antutu --dgpu               Run on discrete GPU"
        echo "  antutu --dgpu --solidtest   Run solid test on discrete GPU"
        ;;
    *)
        # Check if user wants discrete GPU via environment variable
        if [ "$ANTOTU_USE_DGPU" = "1" ]; then
            run_with_dgpu "$1"
        else
            /opt/antutu/bin/benchmark -start "$1"
        fi
        ;;
esac
''' >  ${pkgdir}/opt/antutu/start.sh
    chmod a+x ${pkgdir}/opt/antutu/start.sh
    mkdir -p ${pkgdir}/usr/bin
    ln -s /opt/antutu/start.sh ${pkgdir}/usr/bin/antutu
}
