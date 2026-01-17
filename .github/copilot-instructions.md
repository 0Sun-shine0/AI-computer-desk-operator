# Copilot 使用说明（项目特定指导）

以下说明帮助 AI 编码/自动化助手在此仓库中快速、安全并且可重复地完成常见任务。

- 项目概览：这是一个桌面自动化与训练混合仓库，主要模块位于 `ai_operator/`（智能体、自动化控制、模型、UI）。主入口是 `ai_operator/main.py`，支持 `--mode` 参数（如 `gui`, `test`, `workflow`）。
- 关键集成与依赖：DeepSeek API（通过环境变量 `DEEPSEEK_API_KEY` 或 `ai_operator/core/config.py` 配置）、PyTorch 训练脚本位于 `models/` 与 `scripts/`（例如 `scripts/train_example.py`），UI 使用 PyQt6。

- 快速命令示例：
  - 安装依赖： `pip install -r requirements.txt`
  - 运行 GUI： `python -m ai_operator.main --mode gui`
  - 运行写作工作流示例：
    `python -m ai_operator.main --mode workflow --topic "人工智能发展趋势" --requirements '{"length":"1000字"}'`
  - 训练示例： `python scripts/train_example.py --epochs 1 --batch-size 8`
  - 运行测试： `pytest tests/ -q`

- 代码与配置要点（直接可查文件示例）：
  - `ai_operator/core/config.py`：查找默认配置与 DeepSeek key 的读取方式。
  - `ai_operator/agents/deepseek_agent.py`：与外部 DeepSeek服务交互，参照此文件实现外部 API 调用模式与错误处理。
  - `ai_operator/automation/*`：鼠标/键盘/网页控制器遵循同步命令接口（例如 `mouse_controller.py`、`keyboard_controller.py`），自动化动作应通过 `workflow_manager.py` 协调。
  - `models/training_pipeline.py` 与 `scripts/train_example.py`：展示训练参数 (`--dataset`, `--model-path`, `--save-dir`, `--use-amp`) 的使用约定。

- 项目惯例与约束（只记录可被代码观察到的约定）：
  - 配置优先级：环境变量 > `ai_operator/core/config.py` 中的硬编码设置。
  - CLI 风格：多数脚本使用 argparse 风格的 `--option` 参数。
  - 不要把密钥或大数据样本提交到仓库：仓库已包含 `.gitignore`，`__pycache__/` 已被忽略。
  - 数据存放：示例/原始样本位于 `ai_operator_dataset/`，其中 `samples/` 包含大量演示样本（通常无需保留在源码库中）。

- 修改与清理建议（自动化助手执行时请遵守）：
  1. 优先创建/更新 `.gitignore` 以阻止将大文件、样本或中间结果再次加入版本控制。
  2. 在删除任何数据/样本前，列出要删除的路径供人工确认；删除操作应由单个有意义的 commit 包装并带有描述性的 commit message。
  3. 避免更改训练代码的接口签名（CLI 参数名与返回的 checkpoint 布局），除非有全面回归测试。

- 关于自动提交与推送（CI/开发流程）：
  - 建议步骤：修改 -> 本地运行 `pytest tests/` -> `git add -A` -> `git commit -m "chore: ..."` -> `git push`。
  - 对于仅删除 `ai_operator_dataset/samples/` 或 `model_evaluation_results.json` 的变更，commit message 建议使用 `chore: remove unneeded dataset samples and eval results`。

- 常见快速定位：
  - 入口与模式：`ai_operator/main.py`
  - DeepSeek 集成：`ai_operator/agents/deepseek_agent.py`
  - 训练脚本示例：`scripts/train_example.py`
  - 自动化工作流：`ai_operator/automation/workflow_manager.py` 与 `ai_operator/workflows/writing_workflow.py`
  - 测试目录：`tests/`

如果本文件有遗漏或需要补充更细节（例如：某些函数的调用约定、具体的返回结构示例），请告诉我要补充的文件或样例，我会把它并入该说明文件。
