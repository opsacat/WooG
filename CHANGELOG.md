# 更新日志 (Changelog)

本项目遵循 [Keep a Changelog](https://keepachangelog.com/) 风格，版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

> 原项目 `dooooongyuan/WooG` 已停止更新，本仓库 `opsacat/WooG` 自 2026-10-27 起由社区接手长期维护。

## [0.1.0] - 2026-10-27

### 新增 (Added)
- 维护基础设施初始化：
  - `version.py`：版本号单一真相源（`__version__`）。
  - `CHANGELOG.md`：本文件，记录每次发版变更。
  - `CONTRIBUTING.md`：贡献指南（开发环境、测试、10998 偏移重算流程、代码约定）。
  - `docs/代码体检与维护路线图.md`：代码体检报告与分阶段维护计划。
- 补全 `requirements.txt` 漏声明的依赖 `psutil`、`frida`。

### 说明 (Notes)
- 本版本**不修改任何运行逻辑**，仅为后续持续维护打地基。
- 游戏大版本更新后需重算 10998 端口补丁偏移与 `GameAssembly.dll` 哈希，流程见 `CONTRIBUTING.md`。

[0.1.0]: https://github.com/opsacat/WooG/releases/tag/v0.1.0
