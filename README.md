# Qoder 自动注册工具

## 🌟 本次更新概览

- **模块化重构（GUI 变薄）**
  - **AccountManager** 拆分至 `src/core/account_manager.py`，专职账户数据持久化。
  - **SystemManager** 拆分至 `src/core/system_manager.py`，负责机器码读取/重置与 Qoder 进程管理。
  - **UserSettings** 拆分至 `src/core/user_settings.py`，负责用户设置的持久化管理。
- **项目结构优化**：引入 `src/` 目录组织源代码，提高项目可维护性。
- **邀请注册功能**：支持通过邀请码注册，可获得邀请奖励。
- **收益**：职责更清晰、便于测试与维护；GUI 文件仅保留 UI 与事件绑定。

## 📁 项目结构（更新）

```
QoderTool/
├── run.py                         # 启动脚本
├── requirements.txt               # 依赖列表
├── setup.py                      # 安装配置
├── README.md                     # 项目说明
├── AGENTS.md                     # AI代理指导文档
├── registered-accounts.json       # 账户数据（运行后生成/更新）
├── src/                          # 源代码目录
│   ├── __init__.py
│   ├── config.py                 # 全局配置
│   ├── main.py                   # 主程序（Flet GUI）
│   ├── core/                     # 核心业务模块
│   │   ├── __init__.py
│   │   ├── account_manager.py    # 账户持久化（新）
│   │   ├── system_manager.py     # 机器码/进程管理（新）
│   │   ├── data_generator.py     # 数据生成器
│   │   ├── email_manager.py      # 邮箱管理器
│   │   ├── browser_manager.py    # 浏览器管理器
│   │   ├── registration_service.py # 注册服务
│   │   └── user_settings.py      # 用户设置管理（新）
│   └── utils/                    # 工具模块
│       ├── __init__.py
│       ├── logger.py             # 日志系统
│       ├── validators.py         # 数据验证
│       └── retry.py              # 重试装饰器
├── data/                         # 数据目录
│   └── user_settings.json        # 用户设置数据（运行后生成/更新）
├── docs/                         # 文档目录
│   ├── api/                      # API 文档
│   │   ├── qoder/                # Qoder 平台相关 API
│   │   │   ├── README.md         # Qoder API 说明
│   │   │   └── ...               # 各具体 API 文档
│   │   ├── account_deletion_api.md  # 账户删除 API
│   │   └── aliyun_device_auth_api.md # 阿里云设备认证 API
│   ├── registration-process.md   # 注册流程文档
│   ├── referral-registration.md  # 邀请注册文档
│   └── project-structure.md      # 项目结构说明
├── scripts/                      # 脚本目录
│   └── reset-machineid.ps1       # 重置机器码脚本
└── .github/workflows/release.yml # CI 构建与发布
```

## 🚀 快速开始

### 1. 环境准备（Windows）

```powershell
# 进入项目目录
Set-Location "e:\VSCodeSpace\Qoder\QoderTool"

# 可选：创建并激活虚拟环境（若已存在可跳过）
# python -m venv .venv
# .\.venv\Scripts\Activate.ps1

# 安装依赖
pip install -r requirements.txt

# 安装 Playwright 浏览器（首次必需）
playwright install chromium
```

### 2. 启动应用

```powershell
# 方式一：使用启动脚本
python run.py

# 方式二：直接运行主程序
cd src
python main.py
```

## ⚙️ 配置说明（src/config.py）

- **BrowserConfig**
  - **headless**: 无头模式（默认 True）
  - **timeout**: 超时时间（默认 30000ms）
  - **viewport**: 视窗大小（默认 1920x1080）
  - **user_agent**: 浏览器 UA
- **EmailConfig**
  - **api_base**: 临时邮箱 API 基址（tempmail.plus）
  - **max_retries / retry_delay / verification_timeout**: 邮箱轮询与超时控制
- **RegistrationConfig**
  - **qoder_url**: 注册页面 URL
  - **qoder_referral_url_template**: 邀请注册URL模板
  - **default_referral_code**: 默认邀请码
  - **delay_between_accounts**: 账户间延迟（秒）
  - **max_registration_attempts**: 最大重试次数
- **UIConfig**
  - **window_width/window_height**: 窗口大小
  - **max_log_lines**: 日志最大行数
  - **theme_mode**: 主题模式（默认 "dark"）
- **AppConfig**
  - **accounts_file**: 账户数据文件名（默认 `registered-accounts.json`）

## 🔍 主要功能

- **📧 邮箱管理**：连接测试、验证码提取、多模式正则匹配、智能重试
- **🤖 浏览器自动化**：反检测、智能等待、多选择器、资源清理
- **🔄 注册流程**：分步执行、失败重试、进度跟踪、数据验证
- **📊 数据管理**：JSON 持久化、批量管理、结构化日志
- **🧩 机器码工具（设置页）**：
  - 一键"重置机器码"（无需管理员权限，直接执行）
  - 会更新 `machineid`、`storage.json` 中的 telemetry 字段、`state.vscdb` 中的 `storage.serviceMachineId`（如存在）
- **⚙️ 用户设置**：
  - 自动保存和加载用户配置
  - 支持邀请码设置
  - 配置存储在 `data/user_settings.json` 中

## 🧭 使用指引

1. 在"设置"中配置临时邮箱与注册选项，可先点"测试邮箱连接"。
2. 在"账号管理"中可查看/删除已保存账户，注册成功会自动写入。
3. 在"仪表盘/设置"中可查看/重置机器码信息（如需提权会弹出 UAC）。
4. 在"设置"中可配置邀请码，留空则使用默认邀请码。

## 🔧 故障排除

- **浏览器启动失败**：执行 `playwright install chromium`，检查防火墙/权限。
- **邮箱连接失败**：核对邮箱地址与 EPIN，检查网络/代理。
- **验证码超时**：适当增大 `verification_timeout`，或检查邮箱服务延迟。
- **注册失败**：页面结构可能变更，请更新选择器配置。
- **用户设置未保存**：检查 `data/` 目录权限，确认 `user_settings.json` 文件可写。

## ⚡ 性能建议

- 批量注册时设置适当延迟，监控系统资源。
- 网络不稳定时配置可靠代理并调高重试。
- 日志定期清理，关注内存使用。
- 合理使用无头模式以节省资源。

## 🚀 CI 构建与发布（GitHub Actions）

- 使用 `.github/workflows/release.yml` 构建并发布，无需提交本地打包产物。
- 触发方式：
  1. 推送符合 `v*.*.*` 的标签（如 `v1.0.0`）
  2. 或在 GitHub Actions 页面手动触发（workflow_dispatch）
- 构建：Windows 环境，使用 Flet pack 将 `src/main.py` 打包为 `QoderTool.exe`
- 产物：
  - Release 附件：`QoderTool.exe`
  - 构建工件（artifact）：`QoderTool-windows`

### 运行说明（打包版）
- 若运行可执行文件时提示浏览器缺失，可在一台可用的 Python 环境中执行：
  ```powershell
  playwright install chromium
  ```
  然后再尝试运行应用。

## 🗺️ 版本记录

- **v2.x（当前）**：
  - 模块化重构：AccountManager、SystemManager、UserSettings 拆分
  - 项目结构优化：引入 `src/` 目录
  - 新增邀请注册功能
  - 提升可维护性，GUI 更薄
- **v1.x**：
  - 基础注册功能、临时邮箱支持、浏览器自动化、账户数据存储

## 📄 许可证

MIT License - 详见 LICENSE 文件。

## 🤝 贡献

欢迎提交 Issue 和 Pull Request ！

## ⚠️ 免责声明

本工具仅供学习和研究，请遵守相关网站的服务条款，合理使用。