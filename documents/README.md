# OpenWrt s905x2 Build Guide

This guide covers **only s905x2** builds using ImageBuilder and kernel compilation.

**1. ImageBuilder Workflow**
- Workflow: `.github/workflows/build-openwrt-using-imagebuilder.yml`
- Purpose: Build s905x2 rootfs via ImageBuilder and package firmware.
- Inputs you can change: release branch, kernel version, kernel tags, IP, builder name.

**2. Kernel Workflow**
- Workflow: `.github/workflows/compile-kernel.yml`
- Purpose: Compile s905x2 kernels and upload artifacts.

**3. Local Packaging (s905x2 only)**
- Place your `*rootfs.tar.gz` in `openwrt-armsr/`.
- Run:

```bash
sudo ./remake -k 6.12.y
```

**4. ImageBuilder Customization**
- Script: `config/imagebuilder/imagebuilder.sh`
- Custom files overlay: `config/imagebuilder/files`
Package selection uses a required package list (hardcoded in the script).

**Defaults**
- IP: `192.168.1.1`
- Username: `root`
- Password: `password`
