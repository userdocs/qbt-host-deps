# QBT Host Dependencies

## Cross-compiling Qt6 with CMake

When cross-compiling Qt6 with CMake, you need to meet one of these conditions to successfully build it:

### Option 1: Install QEMU emulation on the host machine (Recommended)

**Note:** This must be done on the host machine, not inside the build container.

#### Debian/Ubuntu-based systems

```bash
sudo apt install qemu-user-static binfmt-support
```

#### Alpine Linux

```bash
sudo apk add qemu qemu-openrc
```

### Option 2: Use prebuilt Qt6 static libraries

If you cannot install QEMU on the host (e.g., when using GitHub Actions `container:`), you can install a custom prebuilt Qt6 static library to `/usr/local` and set `-D QT_HOST_PATH` with CMake when building Qt6.

#### Installation commands

```bash
curl -sLO https://github.com/userdocs/qbt-qt6/releases/latest/download/x86_64-qt6-iconv.tar.xz
tar -xf x86_64-qt6-iconv.tar.xz --strip-components=1 -C /usr/local
ldconfig
```

**Note:** This method works inside Docker containers (like GitHub workflow `container:`) where QEMU cannot be installed on the host.

## Why This Project Exists

### The Problem

Cross-compiling Qt6 presents several challenges:

1. **QEMU Requirement**: Qt6 cross-compilation typically requires QEMU emulation to be available during the build process.

2. **GitHub Actions Limitations**: When using `container:` in GitHub workflows, you cannot modify the host runner, so QEMU emulation is unavailable.

3. **Qt6 Configuration Complexity**: Installing system Qt6 often results in linking configurations that differ from desired static builds. Qt6 has numerous auto-toggles and features that make configuration difficult.

4. **Build Time**: Without proper setup, you're forced to build Qt6 twice during the build process, significantly increasing build time.

### The Solution

This project provides a fallback method that solves these issues:

- **Option 1** (Recommended): Use QEMU emulation for temporary emulation during the build process
- **Option 2** (Fallback): Use prebuilt static Qt6 libraries when QEMU is not available

### Why Not System Qt6?

- System Qt6 installations are often linked differently than required for static builds
- Qt6's auto-configuration can override desired settings
- Dependency management becomes unnecessarily complex
- Build reproducibility is compromised

That's why this project exists - to provide a proper solution for cross-compiling Qt6 without the usual complications.