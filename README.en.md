[简体中文](README.md) | [English](README.en.md)

# openwrt_x86_64-firmware

A personal repo that builds OpenWrt firmware automatically in the cloud with GitHub Actions, adapted from the [P3TERX/Actions-OpenWrt](https://github.com/P3TERX/Actions-OpenWrt) template.

[![LICENSE](https://img.shields.io/github/license/mashape/apistatus.svg?style=flat-square&label=LICENSE)](https://github.com/P3TERX/Actions-OpenWrt/blob/master/LICENSE)
![GitHub Stars](https://img.shields.io/github/stars/P3TERX/Actions-OpenWrt.svg?style=flat-square&label=Stars&logo=github)
![GitHub Forks](https://img.shields.io/github/forks/P3TERX/Actions-OpenWrt.svg?style=flat-square&label=Forks&logo=github)

## 📖 Introduction

The repo stores only build configuration and workflows, not source code. On each build, Actions clones [Lean's OpenWrt (coolsnowwolf/lede)](https://github.com/coolsnowwolf/lede) master branch, configures it with the repo's `.config` plus two customization scripts — `diy-part1.sh` / `diy-part2.sh` (executed before / after feeds update; useful for adding feed sources, changing the default IP, etc.) — then downloads dependencies and compiles in parallel. Firmware artifacts are uploaded to Artifacts, with optional Release publishing.

The current `.config` targets the 8devices Carambola2 board (ath79/generic, squashfs + initramfs). To build x86_64 or another platform, regenerate `.config` locally with `make menuconfig` and commit it.

## ✨ Features

- Manually triggered builds (workflow_dispatch), with optional SSH access to Actions for live debugging (tmate)
- Fully automated pipeline: clone source → load custom feeds and diy scripts → update/install feeds → apply `.config` → `make defconfig` → download dl → parallel compile (auto-fallback retry on failure)
- Artifacts are named by device + date; with `UPLOAD_RELEASE=true` a date-tagged Release is published and only the latest 3 are kept
- Switches for uploading the bin directory and for Cowtransfer / WeTransfer transfers
- `update-checker` workflow: compares the upstream lede HEAD commit and triggers a firmware build via repository_dispatch when the hash changes (requires the `ACTIONS_TRIGGER_PAT` secret)
- Automatic cleanup of old workflow runs

## 🚀 Quick Start

1. Edit `.config` to pick your target platform and packages (generate it locally with `make menuconfig` and upload);
2. Edit `diy-part1.sh` (before feeds update) and `diy-part2.sh` (after feeds update) as needed — both ship commented examples;
3. On the repo's Actions page select **Build OpenWrt** → **Run workflow**; set the `ssh` input to `true` if you need live debugging;
4. Download the firmware from Artifacts (top-right of the Actions page) when the build finishes; to publish a Release automatically, flip env switches such as `UPLOAD_FIRMWARE` / `UPLOAD_RELEASE` to `true`;
5. To build automatically on upstream updates: run the **Update Checker** workflow once and configure the `ACTIONS_TRIGGER_PAT` secret (the schedule cron is commented out by default; enable it yourself).

## 📁 Directory Structure

```
.
├── .config                        # OpenWrt build config (target platform and packages)
├── .github/workflows/
│   ├── build-openwrt.yml          # Firmware build workflow
│   └── update-checker.yml         # Upstream source update checker
├── diy-part1.sh                   # Customization script 1 (before feeds update)
├── diy-part2.sh                   # Customization script 2 (after feeds update)
└── LICENSE
```

## 🔗 Credits

- [Microsoft Azure](https://azure.microsoft.com)
- [GitHub Actions](https://github.com/features/actions)
- [OpenWrt](https://github.com/openwrt/openwrt)
- [Lean's OpenWrt](https://github.com/coolsnowwolf/lede)
- [tmate](https://github.com/tmate-io/tmate)
- [mxschmitt/action-tmate](https://github.com/mxschmitt/action-tmate)
- [csexton/debugger-action](https://github.com/csexton/debugger-action)
- [Cowtransfer](https://cowtransfer.com)
- [WeTransfer](https://wetransfer.com/)
- [Mikubill/transfer](https://github.com/Mikubill/transfer)
- [softprops/action-gh-release](https://github.com/softprops/action-gh-release)
- [ActionsRML/delete-workflow-runs](https://github.com/ActionsRML/delete-workflow-runs)
- [dev-drprasad/delete-older-releases](https://github.com/dev-drprasad/delete-older-releases)
- [peter-evans/repository-dispatch](https://github.com/peter-evans/repository-dispatch)

Build tutorial: [Build OpenWrt with GitHub Actions - P3TERX](https://p3terx.com/archives/build-openwrt-with-github-actions.html) (Chinese)

## 📄 License

[MIT](LICENSE) © [**P3TERX**](https://p3terx.com)
