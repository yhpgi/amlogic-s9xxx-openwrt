# S905X2 ImageBuilder + Kernel Focus Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Refocus the repo to build OpenWrt via ImageBuilder and compile the kernel for s905x2 only, keeping local packaging scripts and removing all other build paths.

**Architecture:** Keep ImageBuilder and kernel workflows, hardcode s905x2 in scripts and action, and prune configs/docs to that single target. Retain local packaging entrypoints but remove board selection inputs.

**Tech Stack:** Bash, GitHub Actions (YAML), OpenWrt ImageBuilder, simple shell tooling (rg, git).

---

### Task 0: Prune Configs to ImageBuilder Only

**Files:**
- Delete: `config/docker`
- Delete: `config/openwrt_main`
- Delete: `config/lede_master`
- Delete: `config/immortalwrt_master`

**Step 1: Write the failing test**

Run: `Get-ChildItem -Force config`

Expected: Directories listed include docker/openwrt_main/lede_master/immortalwrt_master.

**Step 2: Implement minimal changes**

- Delete the four non-imagebuilder config directories listed above.

**Step 3: Run test to verify it passes**

Run: `Get-ChildItem -Force config`

Expected: Only `imagebuilder` (and `README.md`) remain.

**Step 4: Commit**

```bash
git add config

git commit -m "chore: keep only imagebuilder config"
```

### Task 1: Trim Workflows to ImageBuilder + Kernel Only

**Files:**
- Delete: `.github/workflows/build-openwer-docker-image.yml`
- Delete: `.github/workflows/build-openwrt-system-image.yml`
- Delete: `.github/workflows/build-openwrt-using-releases-files.yml`
- Delete: `.github/workflows/build-openwrt-using-unifreq-scripts.yml`
- Modify: `.github/workflows/build-openwrt-using-imagebuilder.yml`
- Modify: `.github/workflows/compile-kernel.yml`

**Step 1: Write the failing test**

Run: `rg -n "openwrt_board" .github/workflows/build-openwrt-using-imagebuilder.yml`

Expected: Matches found (input and usage exist).

**Step 2: Implement minimal changes**

- Remove `openwrt_board` from workflow inputs.
- Remove any `openwrt_board` reference in the `Package OpenWrt` step.
- Update workflow text to state s905x2-only focus.
- In `compile-kernel.yml`, update description/body text to s905x2-only focus (no functional change).
- Delete the four non-scope workflows listed above.

**Step 3: Run test to verify it passes**

Run: `rg -n "openwrt_board" .github/workflows/build-openwrt-using-imagebuilder.yml`

Expected: No matches.

**Step 4: Commit**

```bash
git add .github/workflows

git commit -m "chore: keep only imagebuilder and kernel workflows"
```

### Task 2: Hardcode s905x2 in Action and Packaging Script

**Files:**
- Modify: `action.yml`
- Modify: `remake`

**Step 1: Write the failing test**

Run: `rg -n "\bopenwrt_board\b" action.yml remake`

Expected: Matches found (board input and usage exist).

**Step 2: Implement minimal changes**

- In `action.yml`:
  - Remove the `openwrt_board` input block.
  - Remove any use of `inputs.openwrt_board`.
- In `remake`:
  - Remove `-b` from getopt options and remove the `-b` case handler.
  - Set `make_board="s905x2"` as a hardcoded default.
  - Keep remaining parameter handling intact.

**Step 3: Run test to verify it passes**

Run: `rg -n "\bopenwrt_board\b" action.yml remake`

Expected: No matches.

**Step 4: Commit**

```bash
git add action.yml remake

git commit -m "feat: hardcode s905x2 and drop board parameter"
```

### Task 3: Prune Board Resources to s905x2 Only

**Files:**
- Delete: `make-openwrt/openwrt-files/platform-files/allwinner`
- Delete: `make-openwrt/openwrt-files/platform-files/rockchip`
- Delete: `make-openwrt/openwrt-files/different-files/*`
- Modify: `make-openwrt/openwrt-files/common-files/etc/model_database.conf`

**Step 1: Write the failing test**

Run: `rg -n "s905x2" make-openwrt/openwrt-files/common-files/etc/model_database.conf`

Expected: Multiple s905x2 entries exist alongside non-s905x2 data.

**Step 2: Implement minimal changes**

- Reduce `model_database.conf` to only the s905x2 row(s) needed for the hardcoded build.
- Remove platform files for non-amlogic targets.
- Remove all `different-files` board overrides.

**Step 3: Run test to verify it passes**

Run: `rg -n "s905x2" make-openwrt/openwrt-files/common-files/etc/model_database.conf`

Expected: Only s905x2 entries remain; no other boards present.

**Step 4: Commit**

```bash
git add make-openwrt/openwrt-files

git commit -m "chore: prune board resources to s905x2"
```

### Task 4: Update Docs for s905x2-Only ImageBuilder + Kernel

**Files:**
- Modify: `README.md`
- Modify: `documents/README.md`
- Modify: `config/README.md`
- Delete: `README.cn.md`
- Delete: `documents/README.cn.md`

**Step 1: Write the failing test**

Run: `rg -n "(openwrt_board|lede|immortalwrt|unifreq|docker)" README.md documents/README.md config/README.md`

Expected: Matches found.

**Step 2: Implement minimal changes**

- Rewrite docs to describe only:
  - ImageBuilder workflow
  - Kernel compile workflow
  - Local packaging via `remake` with no board parameter (s905x2-only)
- Remove references to other build modes and other distros.
- Remove CN docs to keep a single, focused documentation set.

**Step 3: Run test to verify it passes**

Run: `rg -n "(openwrt_board|lede|immortalwrt|unifreq|docker)" README.md documents/README.md config/README.md`

Expected: No matches.

**Step 4: Commit**

```bash
git add README.md documents/README.md config/README.md

git add README.cn.md documents/README.cn.md

git commit -m "docs: focus on s905x2 imagebuilder and kernel"
```

### Task 5: Use Required Package List

**Files:**
- Modify: `config/imagebuilder/imagebuilder.sh`
- Modify: `README.md`
- Modify: `documents/README.md`
- Modify: `config/README.md`

**Step 1: Write the failing test**

Run: `rg -n "config_list|custom packages" config/imagebuilder/imagebuilder.sh`

Expected: Matches found (custom package hooks exist).

**Step 2: Implement minimal changes**

- Remove custom package downloads and custom package config usage.
- Build ImageBuilder with the required package list (hardcoded list).
- Update docs to state the required package list is used.

**Step 3: Run test to verify it passes**

Run: `rg -n "config_list|custom packages" config/imagebuilder/imagebuilder.sh`

Expected: No matches referencing custom package selection.

**Step 4: Commit**

```bash
git add config/imagebuilder/imagebuilder.sh README.md documents/README.md config/README.md

git commit -m "chore: use default imagebuilder packages only"
```
