# 京东云亚瑟 (JDC AX1800 Pro) 固件定制与备份项目备忘录 (GEMINI.md)

本文件记录了本项目的核心配置、本地文件结构和后续刷机步骤，以便于在后续的 Antigravity / Gemini 会话中无缝衔接。

## 1. 项目基础信息

* **目标设备**：京东云无线宝亚瑟 (JDC AX1800 Pro / RE-SS-01，256GB eMMC 版)
* **硬件平台代码**：`CONFIG_TARGET_ipq60xx_generic_DEVICE_jdcloud_re-ss-01=y`
* **管理网段**：**`192.168.8.1`** (默认无密码，已在 Actions 编译管道中自动完成修改，防与光猫 IP 冲突)
* **软件源分支**：ImmortalWrt `master` 主分支
* **定制功能配方**：Docker 容器平台、Samba4 局域网共享、AdGuardHome 全屋广告过滤、Diskman 磁盘管理与分区挂载、QuickStart 新手大盘、Argon & Material 双颜值主题。

---

## 2. 本地工作区文件映射表

* **编译配置文件**：
  * [seed.config](file:///E:/%E4%BA%9A%E7%91%9F%20ax1800%20Pro%20%E8%B5%84%E6%96%99%E5%88%86%E4%BA%AB/seed.config)：定制固件的功能配方表。
  * [.github/workflows/build-openwrt.yml](file:///E:/%E4%BA%9A%E7%91%9F%20ax1800%20Pro%20%E8%B5%84%E6%96%99%E5%88%86%E4%BA%AB/.github/workflows/build-openwrt.yml)：GitHub Actions 云编译流水线脚本（已修复动态固件提取路径、优化小包删除逻辑、开启 GITHUB_TOKEN 的 `contents: write` 发布权限）。
  * [git_upload.ps1](file:///E:/%E4%BA%9A%E7%91%9F%20ax1800%20Pro%20%E8%B5%84%E6%96%99%E5%88%86%E4%BA%AB/git_upload.ps1)：关联推送配置一键上传脚本。
* **物理备份文件**：
  * [backup.sh](file:///E:/%E4%BA%9A%E7%91%9F%20ax1800%20Pro%20%E8%B5%84%E6%96%99%E5%88%86%E4%BA%AB/backup.sh)：路由器端底层核心分区备份脚本。
  * [router_clean_backup/](file:///E:/%E4%BA%9A%E7%91%9F%20ax1800%20Pro%20%E8%B5%84%E6%96%99%E5%88%86%E4%BA%AB/router_clean_backup/)：解压后的 20 个底层分区原始镜像文件（救砖安全网）。
* **UI 交互设计**：
  * [luci_md3_theme_design.md](file:///E:/%E4%BA%9A%E7%91%9F%20ax1800%20Pro%20%E8%B5%84%E6%96%99%E5%88%86%E4%BA%AB/luci_md3_theme_design.md)：MD3 极简网页界面设计说明书。
  * [luci_md3_dashboard_minimal.png](file:///E:/%E4%BA%9A%E7%91%9F%20ax1800%20Pro%20%E8%B5%84%E6%96%99%E5%88%86%E4%BA%AB/luci_md3_dashboard_minimal.png)：极简浅色后台界面效果图。

---

## 3. GitHub 仓库与编译链

* **GitHub 远程仓库**：`https://github.com/ZeroEdc/immortalwrt.git`
* **编译执行逻辑**：
  * Actions 云端拉取代码并自动编译。
  * 编译结束后，将自动在仓库的 **Releases** 栏目下，生成包含最新时间戳的 Release 发布包，提供固件下载。

---

## 4. 后续实机操作步骤

1. **下载固件**：Actions 编译绿色对勾后，下载最新 Release 内的两个 `.bin` 固件。
2. **刷入固件**：
   * 拔掉路由器电源，网线连接电脑和路由器 LAN 口。
   * 按住路由器 Reset 键不松开，插上电源，等待 10 秒左右指示灯闪烁。
   * 浏览器访问 `192.168.1.1` 进入不死 U-Boot 控制台。
   * 上传下载好的 **`factory.bin`** 固件执行刷写。
3. **初始化与挂载扩容**：
   * 路由器重启后，浏览器登录管理页面：**`192.168.8.1`** (默认无密码)。
   * 进入 **“服务” -> “磁盘管理”**，对 256GB eMMC 闲置空间进行格式化，挂载为本地 `/overlay` 以便安装插件和 Docker。
