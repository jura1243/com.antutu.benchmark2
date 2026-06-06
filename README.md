# AnTuTu Benchmark for Arch Linux / CachyOS

[![AUR Package](https://img.shields.io/badge/AUR-package-blue)](https://aur.archlinux.org/packages/com.antutu.benchmark)
[![License](https://img.shields.io/badge/license-custom-lightgrey)](LICENSE)

## Overview

**AnTuTu Benchmark** is a cross-platform performance testing tool that evaluates CPU, GPU, MEM, and UX performance. This package provides installation support for Arch Linux and its derivatives (including CachyOS, Manjaro, EndeavourOS).

![AnTuTu Benchmark](https://www.antutu.com/resource/images/antutu_logo.png)

## Features

- ✅ Full hardware performance evaluation
- ✅ CPU, GPU, MEM, and UX benchmarks
- ✅ 3D graphics performance test
- ✅ English interface support
- ✅ Proper desktop menu integration
- ✅ Hybrid graphics support (NVIDIA Optimus / AMD Switchable)

## Installation

### Method 1: Using AUR Helper (Recommended)

```bash
# Using yay
yay -S com.antutu.benchmark

# Using paru
paru -S com.antutu.benchmark

# Using aura
sudo aura -A com.antutu.benchmark
```

### Method 2: Manual Build from Source

```bash
# Clone the repository
git clone https://github.com/jura1243/com.antutu.benchmark.git
cd com.antutu.benchmark

# Build and install the package
makepkg -si

# Or build only
makepkg -c
```

### Method 3: Using the Patch

If you want to apply the fixes to the original PKGBUILD:

```bash
# Download the original PKGBUILD and patch
wget http://file.antutu.com/soft/com.antutu.benchmark_amd64.deb
wget https://raw.githubusercontent.com/jura1243/com.antutu.benchmark/master/com.antutu.benchmark.patch
wget https://raw.githubusercontent.com/jura1243/com.antutu.benchmark/master/fix-desktop-paths.patch

# Apply the patch
patch -p1 < com.antutu.benchmark.patch

# Build the package
makepkg -si
```

## Usage

### Launch from Application Menu

After installation, find "AnTuTu Benchmark" or "安兔兔评测" in your applications menu.

### Launch from Terminal

```bash
antutu
```

### Command Line Options

```bash
# Run benchmark
antutu

# Run with specific test
/opt/antutu/bin/benchmark --solidtest
/opt/antutu/bin/benchmark --graytest
/opt/antutu/bin/benchmark --colortest
```

## Language Settings

The application automatically determines the interface language based on your system locale:

- **Chinese locale** → Chinese interface
- **Any other locale** → English interface (default)

The wrapper script explicitly sets `LANG=en_US.UTF-8` to ensure the English interface is used.

### Changing Language

To run with a different locale:

```bash
# Run with Chinese interface (if available)
LANG=zh_CN.UTF-8 antutu

# Run with Russian locale (will use English interface)
LANG=ru_RU.UTF-8 antutu
```

## Discrete GPU Support

For laptops with hybrid graphics (NVIDIA Optimus or AMD Switchable Graphics), the launcher script supports running the benchmark on the discrete GPU for better performance.

### Using Command Line Options

```bash
# Run on discrete GPU
antutu --dgpu

# Or use short option
antutu -d

# Run specific test on discrete GPU
antutu --dgpu --solidtest
```

### Using Environment Variable

```bash
# Force use of discrete GPU
ANTOTU_USE_DGPU=1 antutu
```

### Supported Technologies

The launcher automatically detects and uses the appropriate method for your system:

| Technology | Package Required | Command |
|------------|------------------|---------|
| **NVIDIA Prime** | `nvidia-prime` | `prime-run` or environment variables |
| **NVIDIA Bumblebee** | `bumblebee`, `primus` | `primusrun` |
| **AMD Switchable** | `mesa-utils` | `DRI_PRIME=1` |

### Manual Configuration

If automatic detection doesn't work, you can manually set environment variables:

**For NVIDIA GPUs:**
```bash
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia __VK_LAYER_NV_optimus=NVIDIA_only antutu
```

**For AMD GPUs:**
```bash
DRI_PRIME=1 antutu
```

### Checking GPU Usage

To verify which GPU is being used:

```bash
# Check NVIDIA GPU usage
nvidia-smi

# Check OpenGL renderer
glxinfo | grep "OpenGL renderer"

# Run with verbose output
antutu --dgpu
```

### Help

```bash
antutu --help
```

Example output:
```
AnTuTu Benchmark Launcher

Usage:
  antutu [OPTIONS] [ARGS]

Options:
  -d, --dgpu, --discrete-gpu  Run on discrete GPU (NVIDIA/AMD)
  -h, --help                  Show this help message

Environment Variables:
  ANTOTU_USE_DGPU=1           Force use of discrete GPU

Examples:
  antutu                      Run with default settings
  antutu --dgpu               Run on discrete GPU
  antutu --dgpu --solidtest   Run solid test on discrete GPU
```

## Package Contents

After installation, the following files are created:

```
/opt/antutu/
├── 3d/                 # 3D benchmark data and Unity engine
├── assets/             # Application assets and translations
├── bin/                # Executable binaries
├── lib/                # Required shared libraries
├── plugins/            # Qt plugins
├── translations/       # Qt translation files
└── start.sh            # Wrapper script for launching

/usr/bin/antutu -> /opt/antutu/start.sh
/usr/share/applications/com.antutu.benchmark.desktop
/usr/share/icons/hicolor/*/apps/com.antutu.benchmark.png/svg
```

## Fixes Applied

This package includes the following fixes compared to the original `.deb` package:

### 1. Desktop Entry Path Fix

**Problem:** The original `.desktop` file contained an incorrect executable path:
```
Exec=/opt/apps/com.antutu.benchmark/files/bin/benchmark
```

**Solution:** Changed to use the wrapper script:
```
Exec=antutu %U
```

The patch `fix-desktop-paths.patch` fixes this issue.

### 2. English Language Support

**Problem:** The application would display in Chinese on systems with Chinese locale.

**Solution:** The wrapper script (`start.sh`) explicitly sets `LANG=en_US.UTF-8` to ensure English interface:
```bash
export LD_LIBRARY_PATH=/opt/antutu:$LD_LIBRARY_PATH
export LANG=en_US.UTF-8
/opt/antutu/bin/benchmark -start $1
```

### 3. Library Path Fix

**Problem:** Missing double slash in library path.

**Solution:** Fixed path from `/opt/antutu//bin/benchmark` to `/opt/antutu/bin/benchmark`.

## Troubleshooting

### Application Doesn't Start from Menu

1. Update the desktop database:
   ```bash
   sudo update-desktop-database
   ```

2. Check the desktop file:
   ```bash
   cat /usr/share/applications/com.antutu.benchmark.desktop
   ```

3. Run from terminal to see errors:
   ```bash
   antutu
   ```

### Missing Dependencies

Install required Qt libraries:
```bash
sudo pacman -S qt5-base qt5-svg
```

### Performance Issues

- Close other applications before running the benchmark
- Ensure your graphics drivers are up to date
- For NVIDIA GPUs, install proprietary drivers: `sudo pacman -S nvidia nvidia-utils`

### Benchmark Results Not Saving

Ensure you have write permissions to the output directory. Results are typically saved in your home directory.

## Uninstallation

```bash
# Remove the package
sudo pacman -R com.antutu.benchmark

# Remove configuration files (optional)
rm -rf ~/.config/antutu
```

## System Requirements

- **Architecture:** x86_64
- **Desktop Environment:** X11 or Wayland
- **Graphics:** OpenGL 3.0+ compatible GPU
- **RAM:** Minimum 4GB (8GB recommended)
- **Storage:** ~500MB free space

## Known Issues

1. **Wayland Support:** The application may have limited functionality under Wayland. X11 is recommended.
2. **NVIDIA GPUs:** Some users report issues with open-source Nouveau drivers. Proprietary NVIDIA drivers are recommended.
3. **3D Test:** The 3D benchmark requires a GPU with OpenGL 3.0+ support.

## Building for Distribution

To build a package for distribution:

```bash
# Clean build
makepkg --cleanbuild

# Build without installing
makepkg -c

# Build with force overwrite
makepkg -f
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This package is distributed under a custom license. AnTuTu is a trademark of AnTuTu.

The original `.deb` package is downloaded from the official AnTuTu website: https://www.antutu.com/

## Acknowledgments

- **AnTuTu** - For providing the benchmark software
- **Arch Linux Community** - For the packaging guidelines
- **AUR Maintainers** - For maintaining the package

## Support

- **Official Website:** https://www.antutu.com/
- **AUR Page:** https://aur.archlinux.org/packages/com.antutu.benchmark
- **Issues:** https://github.com/jura1243/com.antutu.benchmark/issues

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0.591-4 | 2026-03-13 | Added discrete GPU support (NVIDIA/AMD hybrid graphics) |
| 1.0.0.591-3 | 2026-03-13 | Added English language support, fixed desktop paths |
| 1.0.0.591-2 | 2026-03-13 | Fixed desktop entry paths |
| 1.0.0.591-1 | 2026-03-13 | Initial release for Arch Linux |

---

**Made with ❤️ for the Arch Linux Community**
