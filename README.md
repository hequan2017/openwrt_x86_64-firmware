[简体中文](README.md) | [English](README.en.md)

# openwrt_x86_64-firmware

基于 GitHub Actions 云端自动编译 OpenWrt 固件的个人仓库，改造自 [P3TERX/Actions-OpenWrt](https://github.com/P3TERX/Actions-OpenWrt) 模板。

[![LICENSE](https://img.shields.io/github/license/mashape/apistatus.svg?style=flat-square&label=LICENSE)](https://github.com/P3TERX/Actions-OpenWrt/blob/master/LICENSE)
![GitHub Stars](https://img.shields.io/github/stars/P3TERX/Actions-OpenWrt.svg?style=flat-square&label=Stars&logo=github)
![GitHub Forks](https://img.shields.io/github/forks/P3TERX/Actions-OpenWrt.svg?style=flat-square&label=Forks&logo=github)

## 📖 项目介绍

仓库只保存编译配置与工作流，不保存源码。构建时 Actions 自动克隆 [Lean's OpenWrt（coolsnowwolf/lede）](https://github.com/coolsnowwolf/lede) master 分支，按仓库内的 `.config` 与 `diy-part1.sh` / `diy-part2.sh` 两个自定义脚本（分别在更新 feeds 前 / 后执行，可换源、改默认 IP 等）完成配置，再下载依赖并多线程编译，固件产物上传到 Artifact，按需发布 Release。

当前 `.config` 对应 ath79/generic 的 8devices Carambola2 设备（squashfs + initramfs）；想编译 x86_64 或其他平台，本地 `make menuconfig` 重新生成 `.config` 覆盖提交即可。

## ✨ 功能特性

- 手动触发构建（workflow_dispatch），可选开启 SSH 连接 Actions 在线调试（tmate）
- 全自动流水线：克隆源码 → 加载自定义 feeds 与 diy 脚本 → 更新/安装 feeds → 载入 `.config` → `make defconfig` → 下载 dl → 多线程编译（失败自动降级重试）
- 产物按设备名 + 日期命名上传 Artifact；`UPLOAD_RELEASE=true` 时自动打日期 tag 发布 Release，并只保留最新 3 个
- 支持开关上传 bin 目录、Cowtransfer / WeTransfer 中转下载
- `update-checker` 工作流：对比上游 lede 最新 commit，哈希变化时通过 repository_dispatch 自动触发固件构建（需配置 `ACTIONS_TRIGGER_PAT`）
- 自动清理旧的工作流运行记录

## 🚀 快速开始

1. 修改 `.config` 定制目标平台与软件包（本地 `make menuconfig` 生成后上传）；
2. 按需编辑 `diy-part1.sh`（feeds 更新前）与 `diy-part2.sh`（feeds 更新后），脚本内已附示例，取消注释即生效；
3. 到仓库 Actions 页选择 **Build OpenWrt** → **Run workflow**，需要在线调试就把 `ssh` 参数设为 `true`；
4. 构建完成后在 Actions 页右上角 Artifacts 下载固件；要自动发 Release，把工作流 env 中 `UPLOAD_FIRMWARE` / `UPLOAD_RELEASE` 等开关按需改为 `true`；
5. 想跟随上游自动编译：手动跑一次 **Update Checker** 工作流并在 Secrets 配置 `ACTIONS_TRIGGER_PAT`（定时 cron 默认注释关闭，可自行打开）。

## 📁 目录结构

```
.
├── .config                        # OpenWrt 编译配置（决定目标平台与软件包）
├── .github/workflows/
│   ├── build-openwrt.yml          # 固件构建工作流
│   └── update-checker.yml         # 上游源码更新检测
├── diy-part1.sh                   # 自定义脚本 1（更新 feeds 前）
├── diy-part2.sh                   # 自定义脚本 2（更新 feeds 后）
└── LICENSE
```

## 🔗 致谢

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

构建教程参考：[使用 GitHub Actions 云编译 OpenWrt - P3TERX](https://p3terx.com/archives/build-openwrt-with-github-actions.html)

## 📄 License

[MIT](LICENSE) © [**P3TERX**](https://p3terx.com)
