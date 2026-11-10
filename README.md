# AstrBot Heart Monitor / AstrBot 健康监测插件

**语言 / Language：简体中文 | [English](README.en.md)**

> **项目状态：规划阶段，尚不可安装。** 本仓库当前尚未提供完成实机验收的正式插件或 Android APK；以下均为目标规格，不代表功能已经实现。

面向 **Xiaomi Smart Band 9** 的 AstrBot 健康监测项目。已确定采用 **A+B 双通道**：

- **A — Windows BLE 实时心率**：在手环开启“心率广播”后，由原生 Windows 上的 AstrBot 插件直接订阅标准 BLE 心率通知（优先验证服务 `0x180D`、特征 `0x2A37`）；持续采集、断流提示、自动重连。
- **B — Android 历史健康同步**：保留官方 Mi Fitness，通过用户授权的 Health Connect 和独立 **Android Companion** 获取实际可共享的步数、睡眠、运动等历史数据，并安全上传到 AstrBot。**无需 Root，也不修改 Mi Fitness**。
- **统一核心**：SQLite、来源标记、去重、历史统计、中文 WebUI、本人专用 QQ/Bot 查询。一个通道失败不应影响另一个通道或 AstrBot/NapCat。

## 语言范围（已确定）

- **AstrBot WebUI：仅简体中文**；不开发中英文切换或双语界面。
- **GitHub 对外材料：中文 + 英文**；保留成对 README、需求文档，后续面向贡献者的使用、安装、发布和接口文档也提供双语版本。
- Bot 回复及插件配置提示默认中文；**不把 Bot/设置双语作为 V1.0 必达功能**。
- Android Companion 的界面默认中文；是否追加英文 UI 由后续发布规划决定，**不属于 GitHub 文档双语要求**。

## V1.0 计划功能

| 模块 | 目标 |
| --- | --- |
| 实时心率 | Windows BLE 心率广播、持续采集、数据新鲜度、断流/离线、自动重连 |
| 历史健康 | Android Companion + Health Connect 增量同步、离线队列、重试和补传 |
| 可用历史指标 | 以 Mi Fitness 实际写入 Health Connect 的步数、睡眠、运动、距离、热量、运动心率为准 |
| 暂不承诺指标 | 日常心率、血氧、压力等未确认的外部可读数据 |
| 存储 | SQLite，原始心率保留 **90 天**；其他期限见需求规格 |
| 中文 WebUI | 总览、实时监测、历史分析、同步中心、设备管理和设置 |
| Bot / QQ | 仅绑定本人在私聊中查询；AI 只读工具再次校验权限 |
| 可靠性 | 独立通道状态、断线恢复、热重载清理、数据去重与异常隔离 |

**注意**：手环提供“心率广播”菜单不代表电脑已经通过连续通知验收；BLE GATT 订阅、与 Mi Fitness 同时连接、长时间运行必须实机验证。Android 上 Health Connect 的可读数据以真实授权和实际同步为准，不保证所有 Mi Fitness 指标可用。

## 系统组成

1. **AstrBot 原生插件**：唯一 Windows BLE 连接管理器、Android 数据接收接口、SQLite、中文 WebUI、Bot 查询与鉴权。
2. **Android Companion APK（独立交付）**：读取获授权的健康记录，维护离线队列，主动经受保护连接上传。
3. **开源复用**：优先使用 Bleak 与标准 BLE API，审查现有项目的源码和许可证后再决定复用，不需要外部 Windows 桌面程序；不得直接复制许可证不明的代码。

## 兼容性及隐私

- 目标设备：Xiaomi Smart Band 9；通道 A 优先原生 Windows；通道 B 的数据接收部分不依赖 Windows BLE。
- 其他手环、固件版本、Linux/Docker 的 BLE 采集不做未经验证的承诺。
- 数据库使用 AstrBot 持久化目录，不进入仓库。
- 仅已绑定本人可以通过 Bot 私聊访问健康数据；未绑定默认拒绝。WebUI/API 服务端鉴权、Companion 独立凭据、加密传输。
- 没有数据时明确显示缺失/过期；不得将历史读数伪装为实时心率。
- 仅供个人健康信息参考，不用于诊断或紧急医疗监护。

## 开发与验收路线

| 阶段 | 目标 | 状态 |
| --- | --- | --- |
| Phase 0 | 仓库及开源许可审查、AstrBot API 和实机可行性验证计划 | 未验收 |
| Phase 1 | Windows BLE 实际心率订阅、持续采集与重连 | 未开始 |
| Phase 2 | SQLite、生命周期、保留策略 | 未开始 |
| Phase 3 | 中文 WebUI、本人 Bot 查询 | 未开始 |
| Phase 4 | Android Companion、Health Connect、离线同步 | 未开始 |
| Phase 5 | A+B 数据整合、去重与异常隔离 | 未开始 |
| Phase 6 | 72 小时测试、安全与发布验收 | 未开始 |

[完整需求（中文）](docs/REQUIREMENTS.zh-CN.md) · [Full requirements (English)](docs/REQUIREMENTS.en.md)

## 安装和发布

**目前尚无经过验证的可安装版本，也未声明已上架插件市场。** 待硬件、权限、稳定性与兼容性验收后提供插件及 Android APK。

欢迎通过 Issues 提交不包含私人健康数据、QQ ID、设备地址和凭据的问题报告。

[MIT License](LICENSE) © 2026 Polarzen
