在 Windows 上使用 `uv` 管理 Python 项目时，PyTorch 偶尔会出现 DLL 加载错误。常见现象是系统 Python 能正常导入 `torch`，但 `uv` 创建的虚拟环境里导入失败。

## 典型报错

```text
OSError: [WinError 1114] 动态链接库(DLL)初始化例程失败。
Error loading ".venv\Lib\site-packages\torch\lib\c10.dll" or one of its dependencies.
```

## 常见原因

- Python 版本和 PyTorch wheel 不匹配。
- `uv` 解析到了不符合当前平台的包。
- 旧虚拟环境或缓存中存在残留依赖。
- CUDA、CPU 版本的 PyTorch 混装。

## 推荐方案：固定 Python 和 PyTorch wheel

在 `pyproject.toml` 中明确 Python 版本，并指定 PyTorch 官方 wheel。

```toml
[project]
name = "your-project"
version = "0.1.0"
requires-python = "==3.11.*"
dependencies = [
    "torch>=2.6",
    "torchvision",
    "torchaudio"
]

[tool.uv.sources]
torch = { url = "https://download.pytorch.org/whl/cpu/torch-2.6.0%2Bcpu-cp311-cp311-win_amd64.whl" }
torchvision = { url = "https://download.pytorch.org/whl/cpu/torchvision-0.21.0%2Bcpu-cp311-cp311-win_amd64.whl" }
torchaudio = { url = "https://download.pytorch.org/whl/cpu/torchaudio-2.6.0%2Bcpu-cp311-cp311-win_amd64.whl" }
```

然后同步依赖：

```bash
uv sync
```

验证：

```bash
uv run python -c "import torch; print(torch.__version__)"
```

## 替代方案：使用 PyTorch 官方索引

如果不想在 `pyproject.toml` 中写固定 wheel URL，可以直接指定 PyTorch 官方索引安装。

CPU 版本：

```bash
uv pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu
```

如果使用 CUDA，请按自己的 CUDA 版本选择对应索引，不要混装 CPU 和 CUDA wheel。

## 重新创建虚拟环境

如果之前已经反复安装过不同版本，建议删除虚拟环境后重新同步。

macOS 或 Linux：

```bash
rm -rf .venv
uv sync
```

Windows PowerShell：

```powershell
Remove-Item -Recurse -Force .venv
uv sync
```

## 排查顺序

1. 确认 Python 版本：`uv run python --version`
2. 确认 PyTorch 版本：`uv run python -c "import torch; print(torch.__version__)"`
3. 确认安装的是 CPU 还是 CUDA 版本。
4. 删除 `.venv` 后重新 `uv sync`。
5. 仍失败时，改用 PyTorch 官方索引或固定 wheel URL。

## 经验总结

Windows 上的科学计算包会涉及 DLL、编译 ABI 和平台 wheel。遇到 PyTorch 导入失败时，不要只反复重装包，先固定 Python 版本和 PyTorch 来源，通常能更快定位问题。
