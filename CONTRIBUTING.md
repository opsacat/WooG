# 贡献指南 (Contributing)

感谢你参与 **WoG 宝玉助手** 的长期维护！本指南帮助你快速搭好环境、跑通测试，并掌握本项目最关键的维护动作——**游戏大版本更新后的补丁重算**。

## 1. 开发环境

要求 **Python 3.10+**（项目使用 Tkinter 做界面，Windows 自带；其他平台需自行安装 `python3-tk`）。

```bash
# 克隆（fork 后请用自己的仓库地址）
git clone https://github.com/opsacat/WooG.git
cd WooG

# 安装依赖
pip install -r requirements.txt
```

> 依赖说明：`requirements.txt` 已包含 `psutil`、`frida` 等。若导入报错，请确认二者均已安装。

## 2. 代码约定（务必遵守）

- **所有游戏内 JS 只写在 `src/宝玉助手.py`**：这是游戏接口的“唯一真源”。`src/宝玉面板.py` 只做 UI 与调度，通过 `import` 调用接口层，**不要让两套实现各自写一份逻辑**，否则游戏一改就会两边漂移。
- **文件名含中文是有意为之**（原作者约定），不要改名。
- 配置读写使用原子写 + 备份机制，新增配置项请沿用现有封装。
- 凭据（Steam / 市场 API key 等）**绝不入库**，只用本地 `config/*.example` 模板。

## 3. 运行测试

```bash
# 不依赖图形界面的单元测试（server + 端口补丁，共 15 个）
python -m unittest discover -s tests

# 面板相关的 GUI 测试（tests/test_宝玉面板.py）需要桌面环境，本地手动验证即可
```

## 4. 🔧 游戏大版本更新后的必做动作：重算 10998 端口补丁

> 这是本项目**最主要的维护成本来源**，每次 WoG 大更新几乎都要做。

`tools/修复宝玉助手连接.py` 通过修改 `GameAssembly.dll` 开放 10998 端口来建立连接。脚本中**写死了两样东西**，游戏更新后必须重算：

1. **`GameAssembly.dll` 的 SHA256**：用于校验文件确实是预期版本，防止改错文件。
2. **补丁的偏移地址（offset）**：定位到 DLL 中需要 patch 的字节位置。

### 重算步骤

1. 找到当前游戏安装目录下的 `GameAssembly.dll`，计算其 SHA256：
   ```bash
   # Windows (PowerShell)
   Get-FileHash -Algorithm SHA256 GameAssembly.dll
   # 或 Linux/macOS
   sha256sum GameAssembly.dll
   ```
   用新哈希替换脚本里的 `EXPECTED_SHA256`（或类似常量）。

2. 用逆向工具（如 **IDA Free / Ghidra / dnSpy**，按函数定位）找到 `wog://` / Inspector 相关初始化逻辑里**端口号常量**所在位置，记录其文件偏移。
   - 参考原脚本注释，确认要 patch 的字节模式（搜索既有特征码）。
   - 更新脚本中的 `PATCH_OFFSET` 与 `PATCH_BYTES`。

3. **先在本地一个 DLL 副本上验证** patch 后能正常开放 10998 端口、游戏能启动、助手能连上，**再**更新仓库里的常量。

4. 提交时务必在 PR / commit 说明里写明：**对应游戏版本号 + 新 SHA256 + 偏移值**，方便下次 diff。

> 提示：偏移定位是技术活，建议保留一份 `tools/` 下的逆向笔记（可放 `docs/`），记录本次用到的特征码与思路，降低下一次的重复成本。

## 5. 提交流程

1. 从 `main` 切出特性分支：`git checkout -b fix/xxx` 或 `feat/xxx`。
2. 改完跑测试（见第 3 节）。
3. 同步更新 `CHANGELOG.md`（在对应版本下补 `Added/Fixed/Changed`）。
4. 发版时改 `version.py` 的 `__version__`，并打同名 git tag：`git tag v0.x.0`。
5. 提 PR 或直接 push（视你的权限）。

## 6. 许可证

本项目继承原作者的 **Apache 2.0** 许可。任何修改都必须保留原始版权与许可证声明，不得移除 `LICENSE` 文件。
