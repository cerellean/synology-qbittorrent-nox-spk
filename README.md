# Synology qBittorrent-nox SPK（自动构建）

自动从上游拉取最新版 qBittorrent 静态二进制文件，打包成群晖（Synology）DSM 7 可安装的 `.spk` 套件，并自动发布到 Releases。

## 特性

- **自动跟随上游版本**：每次运行从 `userdocs/qbittorrent-nox-static` 获取最新 qBittorrent 版本号，无需手动改版本。
- **版本号自动递增**：套件版本格式为 `{上游版本}-{构建编号}`（如 `5.3.0-42`），每次构建编号自动加一。
- **多架构支持**：同时构建 `x86_64` 与 `aarch64` 两个架构的套件。
- **文件名带架构和版本**：产物命名为 `qBittorrent-{架构}-{版本}-{构建编号}.spk`，下载时一目了然。
- **自动发布 Release**：构建完成后自动创建 GitHub Release 并上传所有 `.spk` 文件。

## 直接下载使用

1. 打开本仓库的 [Releases](https://github.com/cerellean/synology-qbittorrent-spk/releases) 页面。
2. 根据你的群晖 CPU 架构选择对应文件：
   - Intel / AMD 机型（如 DS920+、DS1621+）→ 下载 `x86_64` 版本
   - ARMv8 机型（如 DS223、DS1522+）→ 下载 `aarch64` 版本
3. 在群晖 **套件中心 → 手动安装**，选择下载的 `.spk` 文件安装。

> 安装前请确认 DSM 版本为 **7.0 及以上**。第三方套件安装时群晖会提示来源未知，属正常现象。

## 版本号规则

| 部分 | 来源 | 示例 |
|------|------|------|
| 上游版本 | 上游 `dependency-version.json` 的 `qbittorrent` 字段 | `5.3.0` |
| 构建编号 | GitHub Actions 的 `run_number`，每次运行自动 +1 | `42` |
| 完整版本 | `{上游版本}-{构建编号}` | `5.3.0-42` |

上游发布新版本后，下一次定时或手动触发的工作流会自动拉取新版本号并重新打包，无需手动修改任何文件。

## 自动构建触发方式

- **定时触发**：每周日 00:00 UTC 自动运行一次。
- **手动触发**：进入仓库 **Actions** 页 → 选择 `Build Synology qBittorrent SPK` → 点击 **Run workflow**。

## 仓库结构
.
├── .github/workflows/build.yml # GitHub Actions 工作流
├── INFO # 套件元数据（版本号、架构由工作流动态写入）
├── scripts/
│ └── start-stop-status # 套件启动/停止脚本
└── README.md


## 自定义说明

- **只构建单一架构**：编辑 `build.yml` 中 `strategy.matrix.include`，删掉不需要的架构项。
- **修改定时频率**：编辑 `build.yml` 中 `schedule` 的 cron 表达式。
- **调整最低 DSM 版本**：修改 `INFO` 中的 `os_min_ver` 字段。

## 免责声明

- 本项目仅做自动化打包，qBittorrent 二进制文件来自 [userdocs/qbittorrent-nox-static](https://github.com/userdocs/qbittorrent-nox-static)，套件打包逻辑为个人维护，与群晖官方及 qBittorrent 官方无关。
- 使用第三方套件存在一定风险，请自行评估。建议安装前备份重要数据。
- 本项目不提供任何形式的担保，使用后果自负。
