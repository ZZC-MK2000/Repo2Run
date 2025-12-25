# Repo2Run
<p align="center">
  <img width="150" alt="Repo2Run" src="https://github.com/user-attachments/assets/b7ee9681-d05b-468f-bbef-3040d8c6683b" />
</p>

<p align="center">
  <a href="https://arxiv.org/abs/2502.13681"><img src="https://img.shields.io/badge/cs.SE-arXiv%3A2502.13681-B31B1B.svg"></a>
  <a href="https://opensource.org/licenses/Apache-2.0"><img src="https://img.shields.io/badge/License-Apache_2.0-blue.svg"></a>
</p>

[English](README.md) | 简体中文

## 🚀 最新消息
我们的论文："Repo2Run: Automated Building Executable Environment for Code Repository at Scale" 已被 **NeurIPS 2025** 主会议接收为 **spotlight**！

Repo2Run 是一个基于大语言模型（LLM）的构建代理系统，帮助在容器化环境中管理和自动化构建过程。该项目提供了处理依赖关系、解决冲突以及管理构建配置的工具。

## 😊 主要特性

- 基于 Docker 的沙箱环境，实现隔离构建
- 自动化依赖管理和冲突解决
- 支持 Python 版本管理
- 包依赖的等待列表和冲突列表管理
- 错误格式处理和输出收集

## 📋 前置要求

- Python 3.x
- Docker
- Git

## 📦 安装步骤

1. 克隆仓库：
```bash
git clone https://github.com/bytedance/repo2run.git
cd repo2run
```

2. 安装所需依赖：
```bash
pip install -r requirements.txt
```

## 🔧 使用方法

主入口是通过构建代理的主脚本。你可以使用以下参数运行它：

```bash
python build_agent/main.py --full_name <repository_full_name> --sha <sha> --root_path <root_path> --llm <llm_name>
```

参数说明：
- `repository_full_name`: 仓库的完整名称（例如：user/repo）
- `sha`: 提交的 SHA 值
- `root_path`: 构建过程的根路径
- `llm_name`: 用于配置的 LLM 模型名称（默认：gpt-4o-2024-05-13）

## 🔍 重要说明
💡 例如，你可以使用以下仓库——它相对容易设置——来验证运行是否存在任何问题。我已经确认它可以在几个主流模型上成功配置，包括 GPT-4o 和 Claude 3.5。

```bash
python build_agent/main.py --full_name "Benexl/FastAnime" --sha "677f4690fab4651163d0330786672cf1ba1351bf" --root_path . --llm "gpt-4o-2024-05-13"
```

你可以使用这个相对容易配置的仓库作为基准，来评估你选择的模型是否能有效处理此类任务。如果整个程序成功启动，相应的仓库内容将保存在 `utils/repo` 下，并将创建一个 `output` 文件夹，其结构如下：
- `inner_commands.json`
- `output_commands.json`
- `pip_list.json`
- `pipdeptree.json`
- `pipdeptree.txt`
- `sha.txt`
- `track.json`
- `track.txt`

如果你成功配置了仓库，将会有以下文件：
- `Dockerfile`
- `code_edit.py`
- `test.txt`

请注意：如果 `output` 文件夹不包含轨迹文件（如 `track.json`），表明执行过程中出现了问题。你可以先自行检查；如果出现其他问题，欢迎在 GitHub 上提交 Issue。

## 🏗️ 项目结构

- `build_agent/` - 主包目录
  - `agents/` - 构建配置的代理实现
  - `utils/` - 工具函数和辅助类
  - `docker/` - Docker 相关配置
  - `main.py` - 主入口点
  - `multi_main.py` - 多进程支持

## 🔍 功能详解

### 1. 基于 Docker 的沙箱环境
项目使用 Docker 容器创建隔离的构建环境，确保干净和可重现的构建。

### 2. 自动化依赖管理
- **等待列表**：管理包安装队列
- **冲突解决**：处理包之间的版本冲突
- **错误处理**：格式化和处理构建错误

### 3. Python 版本管理
支持多个 Python 版本的构建环境。

### 4. 配置代理
利用 GPT 模型协助构建配置和问题解决。

## 🔧 如何贡献
如果你想修改 Repo2Run 以更好地满足你的需求，我们已经概述了一些潜在的改进计划。由于时间限制，我们可能无法立即完成这些更改。但是，如果你实现了其中任何一个，我们热烈欢迎你提交 PR 并为项目做出贡献！

1. Fork 仓库
2. 创建你的特性分支 (`git checkout -b feature/amazing-feature`)
3. 提交你的更改 (`git commit -m 'Add some amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 打开一个 Pull Request

## 🙋 常见问题

我们收集了一些常见问题供你参考。如果遇到未涵盖或未解决的问题，欢迎提交 Issue。

### 1. 程序无法启动，或下载仓库后无法进入下一步
答：我建议首先运行我们推荐的示例来验证你的工作流程是否可以端到端运行。如果你的 `output` 文件夹中没有生成 `track.json` 等文件，通常是环境配置问题。请检查 Docker 是否正确启动。

### 2. 程序运行，但模型不断抛出错误："ERROR! Your reply does not contain valid block or final answer"
答：这个错误来自 `agents/configuration.py`，它检查 LLM 的回复是否包含用三个反引号 ``` 包装的命令结构。实际上，我们在提示中明确指定了所需的输出格式；至少在我们的测试中，GPT-4o 和 Claude-3.5-Sonnet 没有出现这个问题。如果你遇到这个问题，我们建议首先检查 LLM 的原始输出（例如 `track.json` 或 `track.txt`）。

### 3. 容器内的 Docker 下载速度太慢，如何设置代理
答：你可以修改位于 `utils/sandox.py` 中 `Sandbox` 类的 `generate_dockerfile` 函数。它管理初始 Dockerfile 的生成。你可以添加类似 `ENV http_proxy=XXX` 的语句来配置网络代理。

## 🔧 未来改进计划
（我们会在时间允许的情况下进行这些工作；非常欢迎 PR）

### 1. 系统提示词的适应性
  - 你可以在 `configuration.py` 中的 `Configuration` 类中修改系统提示词。当前的提示词针对 GPT-4o 定制，可能不适合其他模型（例如，较小的模型可能超出上下文限制）。

### 2. 多语言支持（Python 之外）
  - 当前版本支持 Python。要添加其他语言，主要步骤是：
    - a. 修改提示词
    - b. 在 `tools` 中添加相应的包管理工具（参考 `apt_download.py` 和 `pip_download.py`）
    - c. 更改基础镜像

#### 参考表：
| 语言 | Docker 基础镜像 | 安装工具 |
| --- | --- | --- |
| Python | python:[version] | pip |
| JavaScript/TypeScript | node:[version] | npm |
| Java | openjdk:[version] | maven |
| Rust | rust:[version] | cargo |
| Ruby | ruby:[version] | bundler |
| R | r-base:[version] | install.packages |
| Go | golang:[version] | go get |
| PHP | php:[version] | composer |

### 3. 重新思考"成功配置"的信号
  - 目前，成功被狭义定义为：所有测试都必须可运行（即 `pytest --collect-only` 不报错）。实际上，许多仓库包含本质上失败或不可运行的测试，这会阻止配置成功。
  - 我们认为这可以改进。如果你想定制标准，请修改 `tools/runtest.py` 和 `tools/poetryruntest.py`。
  - 这部分可以灵活处理，例如：
    - 更严格：要求测试通过
    - 更宽松：只要求 80% 的测试运行，或通过特定测试等。

### 4. 更多改进 Repo2Run 的潜在领域...

## 🔗 引用

```bibtex
@article{hu2025repo2run,
  title={Repo2Run: Automated Building Executable Environment for Code Repository at Scale},
  author={Hu, Ruida and Peng, Chao and Wang, Xinchen and Xu, Junjielong and Gao, Cuiyun},
  journal={arXiv preprint arXiv:2502.13681},
  year={2025}
}
```

附：[论文维护者和作者](https://kinesiatricssxilm14.github.io/)是一名在读硕士研究生。由于该项目主要由一个人实现和维护，各种 bug🐛 是不可避免的。非常欢迎你与我讨论该项目。

## 🔗 许可证

Apache-2.0

## 致谢

[https://github.com/Aider-AI/aider](https://github.com/Aider-AI/aider)

## 联系方式

pengchao.x@bytedance.com
