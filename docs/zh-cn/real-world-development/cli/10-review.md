---
title: "第 10 课 - 回顾与后续步骤"
description: "回顾 Copilot CLI 工作流、两个拉取请求、可复用自定义项和更多资源。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

你已在 Tailspin Toys 的完整工作流中使用 GitHub Copilot CLI。本次研讨会中，你：

- 准备了 Codespace、安装 Copilot CLI、探索项目，并找到预置的筛选议题。
- 添加星级评分，在转发的浏览器中检查结果，并手动合并了第一个拉取请求（PR）。
- 根据筛选议题开始工作，在 Plan 模式下确定方案，在 Autopilot 模式下实现，并在 Interactive 模式下审查。
- 使用自定义指令指导智能体、自定义现有 `quality-checks` 技能，并使用它运行单元测试、lint 和类型检查。
- 添加 Playwright Model Context Protocol (MCP) 服务器，并用它在真实浏览器中体验筛选功能。
- 创建并选择质量保证（QA）自定义智能体，以评估要求、覆盖率、技能结果和浏览器证据。
- 审查完整筛选更改，并授权 Agent Merge 处理筛选 PR。
- 探索了用于上下文、模型、共享和可选云端委派的斜杠命令。

## 已交付的内容

本研讨会有两个 PR 阶段，每个阶段都基于更新后的 `main` 创建独立分支：

1. **星级评分**：在游戏卡片上显示现有的 `starRating` 和明确的未评分状态。
2. **筛选和质量工作流**：实现筛选、更新指令并将其应用到功能、自定义 `quality-checks` 报告、创建 QA 配置文件，并包含相关测试。

从规划筛选功能到创建 PR，你一直使用同一对话和分支。为了简化研讨会，这些工作合并为一个 PR。

## 不同类型的验证

你通过自动化测试、浏览器手动检查和 Copilot 使用 MCP 进行的浏览器探索检查了代码。`quality-checks` 技能运行单元测试、lint 和类型检查，并按新格式报告结果。QA 将这些结果与要求和测试覆盖率审查结合起来，为 PR 提供检查依据。

新增测试应解决实际覆盖缺口；QA 运行后认为无需新增测试也可能是正确的。授权合并前，请审查代码和证据；更改后应重新生成受影响的证据。

## 最佳实践

提供给 Copilot 的上下文和工具会影响它的工作。在本研讨会中，你更新了指令、自定义技能、创建 QA 配置文件并配置 MCP 服务器。可以在多个对话中复用这些自定义项，并根据团队需要进行调整。指令定义标准，技能描述可重复任务，自定义智能体定义专家角色，MCP 服务器连接外部工具。请审查实际更改和工具结果，而不只是智能体的摘要。

根据任务选择**模式和模型**。使用 **Plan** 先思考方案，使用 **Interactive** 跟进聚焦的小改动，使用 **Autopilot** 处理范围明确的任务。日常修改可选择更快的模型；复杂工作可选择能力更强的模型。

上下文仍与基础设施同样重要。清楚说明要构建**什么**、**为什么**以及**如何**构建，会显著影响结果。

## 更多可探索内容

核心工作流已完成。还可以了解一些 CLI 功能：

- `/review`：让代码审查智能体分析更改。
- `/rubber-duck`：讨论问题并获得另一种观点。
- `/fleet`：并行协调彼此独立的子任务。
- `/worktree`：隔离单独任务。
- `/delegate`：将任务交给 Copilot 云智能体。

## 后续步骤

提高工具使用能力的最佳方式就是持续使用它。可以将 Copilot 用于生产代码、个人项目，或一直想构建却尚未着手的小应用。与团队分享经验、互相学习，并持续探索文档。

如需从终端扩展 Tailspin Toys，可继续学习可选的 [Foundry Backer Concierge 系列][foundry]。也可以比较其他环境：[VS Code 研讨会][vscode]、[GitHub Copilot app 研讨会][app]或 [Copilot 云智能体研讨会][cloud]。

## 资源

- [关于 GitHub Copilot CLI][about-cli]
- [Copilot CLI 命令参考][cli-reference]
- [自定义 Copilot CLI][customize-cli]
- [使用 Copilot CLI 管理拉取请求][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
