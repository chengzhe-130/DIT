# AGENTS.md

本文件为 AI 编码助手（Agent）在此仓库中工作提供指导。请先通读全文再开始改动。

## 项目概述

本仓库是论文 **"Scalable Diffusion Models with Transformers"（DiT）** 的官方 PyTorch 实现。核心思想是：用 Transformer 主干替代扩散模型中常用的 U-Net，在 ImageNet 的类条件图像生成任务上取得优异的 FID 指标，并具有良好的可扩展性（Gflops 越高，FID 越低）。

- 论文: http://arxiv.org/abs/2212.09748
- 项目主页: https://www.wpeebles.com/DiT
- 许可证: CC-BY-NC（见 `LICENSE.txt`）

## 仓库结构

```
models.py               # DiT 模型定义（核心）
train.py                # 基于 PyTorch DDP 的训练脚本
sample.py               # 单 GPU/CPU 采样脚本（使用预训练权重或自定义 ckpt）
sample_ddp.py           # DDP 大规模并行采样脚本，生成 .png 及用于 FID 评估的 .npz
download.py             # 预训练权重下载与本地 ckpt 加载工具
diffusion/              # 高斯扩散流程（改自 OpenAI 的 GLIDE / ADM / IDDPM）
  ├── __init__.py       # create_diffusion() 入口
  ├── gaussian_diffusion.py
  ├── respace.py
  ├── diffusion_utils.py
  └── timestep_sampler.py
environment.yml         # Conda 环境定义
run_DiT.ipynb           # Colab 演示笔记本
README.md               # 项目说明
CONTRIBUTING.md
CODE_OF_CONDUCT.md
```

被 `.gitignore` 忽略的目录：`pretrained_models/`、`datasets/`、`results/`、`__pycache__`。这些目录在运行时由脚本自动创建，不要将它们提交到版本控制。

## 版本控制

本项目使用 Git 进行版本管理。修改或添加任何程序时，必须执行 commit 然后 push。具体要求如下：

- **提交粒度**: 每完成一个逻辑上独立的改动（修复一个 bug、新增一个功能、重构一个模块）就应单独提交一次，不要把多个无关的改动堆在同一次提交里。
- **提交信息**: 使用简洁且能说明意图的 commit message，推荐格式为 `<类型>: <简述>`，类型如 `feat`（新功能）、`fix`（修复）、`refactor`（重构）、`docs`（文档）、`chore`（杂项）等。例如 `feat: 添加 Flash Attention 支持`、`fix: 修复 DDP 采样时 rank 同步问题`。
- **立即推送**: 每次提交后立即执行 `git push`，确保远程仓库与本地保持同步，避免本地积压过多未推送的提交。
- **提交卫生**: 不要提交运行时产物，包括 `pretrained_models/`、`datasets/`、`results/`、`samples/`、`__pycache__`、`*.pt` 权重文件、`sample.png` 等生成图像。这些已在 `.gitignore` 中忽略，新增的临时产物也应加入忽略列表。
- **不要提交敏感信息**: 禁止将密钥、凭证、个人数据路径等提交到仓库。
- **操作流程**: `git add <相关文件>` -> `git commit -m "<信息>"` -> `git push`。若提交失败（如被 hook 拦截），应修复问题后重新提交，而非使用 `--no-verify` 跳过检查。

