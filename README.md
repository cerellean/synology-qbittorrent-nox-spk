# Synology qBittorrent SPK (Auto Build)

本项目提供了一个基于 GitHub Actions 的全自动构建方案，用于为群晖（Synology）DSM 7 打包最新版的 qBittorrent 套件（`.spk`）。

**核心说明：** 本项目的所有群晖适配配置（包括安装向导、权限控制、WebUI 资源、保活脚本及默认配置文件等）均**提取自“矿神”（矿神SPK7）最终编译发布的版本**。在此基础上，自动化流水线会拉取上游最新版 `qbittorrent-nox` 静态二进制文件进行重新组合，真正做到**界面完美、后台稳定、一键更新**。

## ✨ 特性

- **原汁原味的群晖体验**：完美复刻了原版套件的安装向导（Wizard），安装时即可设置 WebUI 账号和密码。
- **全自动构建**：每周日自动触发（或手动触发），自动检查上游最新版本并打包。
- **免配置端口**：内置优化后的 `qBittorrent.conf` 配置文件，默认 WebUI 端口为 **8085**，安装即用，无需 SSH 进入后台手动修改。
- **稳定保活**：集成 `qbmonitor` 守护进程，自动修复配置、监控进程状态，异常退出自动重启。
- **多架构支持**：自动同时构建 `x86_64`（Intel/AMD）与 `aarch64`（ARMv8）两个版本。
- **版本自动递增**：套件版本格式为 `{上游最新版}-{构建编号}`（如 `5.3.0-14`），每次构建自动递增。

## 🚀 直接下载使用

1. 前往本仓库的 [Releases](../../releases) 页面。
2. 根据你的群晖 CPU 架构下载对应的 `.spk` 文件：
   - **Intel / AMD 机型**（如 DS920+, DS1621+）→ 下载 `x86_64` 版本
   - **ARMv8 机型**（如 DS223, DS1522+）→ 下载 `aarch64` 版本
3. 打开群晖 **套件中心 → 手动安装**，选择下载的 `.spk` 文件。
4. 按照向导提示，设置你的 qBittorrent WebUI 账号和密码。
5. 安装完成后，在浏览器访问：`http://你的群晖IP:8085`。

> 安装前请确保你的群晖系统为 **DSM 7.0 及以上**。第三方套件安装时群晖会提示来源未知，选择“是”继续安装即可。

## 🛠️ 构建原理与仓库结构

本仓库通过 GitHub Actions 自动完成以下工作：
1. 获取上游 qBittorrent 的最新版本号，并更新 `INFO` 文件。
2. 下载对应架构的纯静态二进制文件 `qbittorrent-nox`。
3. 将最新二进制文件、提取的 `qbmonitor` 守护脚本以及 `ui` 资源目录合并打包成 `package.tgz`。
4. 将 `defaults/qBittorrent.conf` 注入到 `package.tgz` 内的正确路径中，确保首次启动不报错。
5. 将程序包与群晖控制文件（`INFO`、`scripts/`、`conf/`、`WIZARD_UIFILES/`）打包成最终的 `.spk` 并发布。

**仓库核心结构：**

```text
.
├── .github/workflows/build.yml   # GitHub Actions 自动构建流
├── bin/
│   └── qbmonitor                 # 守护与保活脚本（提取自矿神SPK）
├── conf/                         # 群晖权限与端口配置文件（提取自矿神SPK）
├── defaults/
│   └── qBittorrent.conf          # 预置的默认配置文件，包含端口 8085（提取自矿神SPK）
├── scripts/
│   ├── service-setup             # 启动/安装环境变量定义（提取自矿神SPK）
│   └── start-stop-status         # 套件启停控制脚本（提取自矿神SPK）
├── ui/                           # WebUI 静态资源文件夹（提取自矿神SPK）
├── WIZARD_UIFILES/               # 安装向导配置文件（提取自矿神SPK）
├── INFO                          # 套件元数据
└── README.md
```

## ⚙️ 自定义说明

如果你 Fork 了本项目并希望进行修改：

- **修改默认端口**：编辑 `defaults/qBittorrent.conf`，修改 `WebUI\Port=8085`；同时修改 `INFO` 文件中的 `adminport="8085"`。
- **修改自动构建频率**：编辑 `.github/workflows/build.yml` 中的 `schedule -> cron` 表达式。
- **关闭 ARM 架构构建**：如果你仅需要 x86 架构，删除 `build.yml` 中 `strategy.matrix.include` 下的 aarch64 配置项即可。

## 📄 License

本项目采用 [MIT License](LICENSE) 授权。

## ⚠️ 免责声明

- 本项目仅作为群晖套件的自动化打包脚本开源，不提供任何形式的担保。
- qBittorrent 二进制文件来自 [userdocs/qbittorrent-nox-static](https://github.com/userdocs/qbittorrent-nox-static)，遵循其上游 GPLv2+ 许可证。
- **本项目的向导、UI、脚本及相关配置等组件提取自互联网（原版 SPK / 矿神SPK7），版权归原作者（矿神）所有。** 本项目仅作学习和自动化流程交流使用，请勿用于商业用途。
- 使用第三方套件存在一定风险，请自行评估。建议安装前备份重要数据。
