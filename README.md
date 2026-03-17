# OpenWrt s905x2 ImageBuilder + Kernel

This repository is focused on one target only: **s905x2**. It builds OpenWrt using **ImageBuilder** and compiles the kernel, with local packaging scripts kept for s905x2 only.

**Workflows**
1. ImageBuilder build: `.github/workflows/build-openwrt-using-imagebuilder.yml`
2. Kernel compile: `.github/workflows/compile-kernel.yml`
3. Cleanup: `.github/workflows/delete-older-releases-workflows.yml`

**Local Packaging (s905x2 only)**
1. Install deps (Ubuntu 24.04):

```bash
sudo apt-get update -y
sudo apt-get full-upgrade -y
sudo apt-get install -y $(cat make-openwrt/scripts/ubuntu2404-make-openwrt-depends)
```

2. Create `openwrt-armsr` and place `*rootfs.tar.gz` inside.
3. Run packaging (board is fixed to s905x2):

```bash
sudo ./remake -k 6.12.y
```

**Local Packaging Parameters**
- `-r` kernel repo (default `ophub/kernel`)
- `-u` kernel tag suffix (default `stable`)
- `-k` kernel version(s) (e.g., `6.12.y` or `6.12.y_6.18.y`)
- `-a` auto-kernel (default `true`)
- `-p` OpenWrt IP (default `192.168.1.1`)
- `-s` image size (e.g., `256/1024` or `1024`)
- `-n` builder signature

**Kernel Build**
Use `.github/workflows/compile-kernel.yml` to build kernels for s905x2 only. Example snippet:

```yaml
- name: Compile the kernel
  uses: ophub/amlogic-s9xxx-armbian@main
  with:
    build_target: kernel
    kernel_version: 6.12.y
    kernel_auto: true
    kernel_sign: -yourname
```

**ImageBuilder Config**
ImageBuilder customization lives in `config/imagebuilder`.