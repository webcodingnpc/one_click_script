# 项目规范

## 项目概述

本项目 `one_click_script` 是一个 **Linux VPS 自动化运维工具包**，提供一键安装脚本解决 VPS 常见运维需求。由 JinWYP 开发，MIT 许可证。

- 所有脚本使用 **Bash/Shell** 编写
- 单文件架构，每个脚本是一个独立的功能模块
- 支持主流 Linux 发行版：CentOS 7+ / Debian 10+ / Ubuntu 16.04+

## 脚本清单

| 脚本 | 路径 | 功能 | 行数 |
|------|------|------|:----:|
| install_kernel.sh | `install_kernel.sh` | 内核管理（安装/切换 Linux 内核），BBR/BBR Plus/BBR2 加速，WireGuard + Cloudflare WARP | ~3800 |
| trojan_v2ray_install.sh | `trojan_v2ray_install.sh` | 代理搭建（trojan/trojan-go/v2ray/xray），SSL 证书，Nginx SNI 分流，AdGuard Home，Mosdns，V2board 服务端 | ~8000+ |
| linux_install_software.sh | `linux_install_software.sh` | 服务器工具安装（Oh My Zsh、Docker、Node.js、PM2、Portainer、CasaOS、Alist、Nginx 等） | ~4000 |
| netflix_check.sh | `netflix_check.sh` | 流媒体解锁检测（Netflix/Disney+/YouTube），WARP IP 刷新 | ~650 |
| bench.sh | `bench.sh` | VPS 基准测试（Speedtest + I/O 性能） | ~100 |

## 目录结构

```
one_click_script/
├── install_kernel.sh           # 内核 & BBR & WARP 脚本
├── trojan_v2ray_install.sh     # 代理/VPN 全能脚本
├── linux_install_software.sh   # 服务器工具脚本
├── netflix_check.sh            # 流媒体检测脚本
├── bench.sh                    # VPS 基准测试脚本
├── README.md                   # 英文主文档（内核/BBR）
├── README2.md                  # 英文文档（trojan/v2ray）
├── README2_CN.md               # 中文文档（trojan/v2ray）
├── KERNEL_CN.md                # 中文文档（内核/BBR）
├── TOOL.md                     # 英文文档（工具脚本）
├── TOOL_CN.md                  # 中文文档（工具脚本）
├── DNS.md                      # DNS/DoH/DoT 配置指南
├── HOWTOUSEGO.md               # Go 软件使用教程
├── acuteangle/                 # 锐角云 PVE 安装工具
│   ├── readme.md               # 使用说明
│   ├── autorun                 # SystemRescue 自动安装脚本
│   └── date.sh                 # BIOS 时间修复脚本
├── dsm/                        # Proxmox VE & 群晖 DSM 工具
│   ├── readme.md               # 使用文档
│   ├── pve.sh                  # PVE/DSM 多功能脚本
│   ├── openwrt.sh              # OpenWRT Mosdns 安装
│   ├── FixSynoboot.sh          # 群晖引导修复
│   └── mosdns.yaml             # Mosdns 配置文件
├── download/                   # 预打包的工具包
├── docs/                       # 文档图片
└── dev-pro/                    # 开发进度跟踪
```

## 开发规范

### Shell 脚本规范
- 使用 `#!/bin/bash` shebang
- 函数命名：驼峰式（如 `installKernel`、`enableBBRSysctlConfig`）
- 变量命名：小写+下划线（如 `osRelease`、`linuxKernelToInstallVersion`）
- 全局变量在文件顶部声明，局部变量使用 `local`
- 颜色输出：使用 `red()`/`green()`/`yellow()`/`blue()` 函数统一管理

### 代码质量
- 函数职责单一，每个函数只做一件事
- 公共功能提取为独立函数（如 `downloadFile()`、`versionCompareWithOp()`）
- 系统检测逻辑统一在启动时调用（`getLinuxOSRelease()` 等）

### 兼容性保持
- 支持 CentOS 7+ / Debian 10+ / Ubuntu 16.04+
- 区分发行版使用不同包管理器（yum/apt-get）
- 架构检测：`checkArchitecture()` 函数统一处理

### 错误处理
- 使用 `$?` 检查命令执行状态
- 关键操作前使用 `promptContinueOpeartion()` 确认
- 全局变量 `sudoCmd` 处理权限提升

### 文档规范
- README 文件包含：功能说明、安装方法、使用说明
- 中文文档后缀 `_CN.md`，英文文档无后缀
- 图片统一放在 `docs/` 目录

### Git 提交
- 提交信息使用中文
- 格式：`类型: 描述`，如 `功能: 新增内核安装支持`、`修复: 修复BBR检测逻辑`、`优化: 重构安装流程`

## 关键架构说明

### BBR 加速系统
- BBR 检测：`lsmod | grep bbr` + `sysctl net.ipv4.tcp_congestion_control`
- 内核需 ≥ 4.9 才能支持 BBR
- BBR Plus 需要专用内核（由 UJX6N/cx9208 编译）
- 队列算法支持：FQ / FQ-Codel / FQ-PIE / CAKE

### 代理系统（trojan_v2ray_install.sh）
- Nginx SNI 分流，多域名 HTTPS 站点共存
- Xray XTLS + VLESS 协议支持
- SSL 证书：acme.sh（支持 Let's Encrypt / BuyPass / ZeroSSL / Google）
- 随机端口生成确保安全性

### 系统优化
- sysctl 优化参数集中管理（`addOptimizingSystemConfig()`）
- 全局错误捕获：uncaughtException + unhandledRejection → 写入 errors/ 目录
