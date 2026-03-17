# Design: S905X2-Only ImageBuilder + Kernel Focus

Date: 2026-03-17

## Goal
Refocus the repository to only build OpenWrt using ImageBuilder and compile the kernel, strictly for the s905x2 target. Remove other build paths, configs, and docs. Keep local packaging scripts.

## Scope
- Keep local packaging scripts (`remake`, `make-openwrt/`) but hardcode s905x2 and remove board parameters.
- Keep GitHub Actions workflows only for:
  - ImageBuilder build
  - Kernel compile
  - Delete older releases
- Keep only ImageBuilder-related configuration.
- Update documentation to describe only ImageBuilder + kernel + local packaging for s905x2.

## Non-Goals
- Supporting other boards.
- Retaining legacy build paths (OpenWrt source builds, Docker builds, unifreq scripts).
- Changing the actual OpenWrt ImageBuilder or kernel sources.

## Architecture
The repo becomes a focused pipeline for producing s905x2 OpenWrt images using ImageBuilder and compiling the kernel. All other build paths and configs are removed to reduce maintenance and ambiguity.

## Components
- Scripts:
  - `remake` and scripts under `make-openwrt/` remain, but s905x2 is hardcoded and board parameters removed.
- Workflows:
  - Keep `build-openwrt-using-imagebuilder.yml` and hardcode s905x2; remove board inputs.
  - Keep `compile-kernel.yml` and hardcode s905x2; remove board inputs.
  - Keep `delete-older-releases-workflows.yml` unchanged.
  - Remove all other workflows.
- Config:
  - Keep `config/imagebuilder` only; remove other config directories.
- Docs:
  - Rewrite `README.md` and `documents/README.md` (and CN variants if kept) to only describe ImageBuilder + kernel + local packaging for s905x2.

## Data Flow
- ImageBuilder:
  - Workflow or local script consumes ImageBuilder tarball + package list.
  - Applies `config/imagebuilder` customization.
  - Produces OpenWrt images for s905x2.
- Kernel:
  - Workflow builds kernel for s905x2 only.
  - Artifacts are published/consumed for packaging as needed.

## Error Handling
- Board selection is hardcoded to s905x2; no board parameter exists.
- Missing inputs (ImageBuilder tarball, kernel version) cause explicit failure with clear messages.

## Testing and Verification
- Validate YAML workflows still parse and have no dangling inputs.
- Quick sanity checks of scripts to ensure no board parameter is referenced.
- No full builds unless explicitly requested.

## Planned Deletions (High Level)
- Workflows: all except ImageBuilder, kernel compile, delete older releases.
- Config: remove `config/docker`, `config/openwrt_main`, `config/lede_master`, `config/immortalwrt_master`.
- Docs: remove unrelated guides and references to non-ImageBuilder build paths or other distributions.
