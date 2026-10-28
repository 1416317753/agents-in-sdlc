---
title: "第 8 课 - 创建并合并功能 PR"
description: "一起审查筛选功能、指令、技能更新、QA 配置文件和测试，然后创建 PR 并使用 Agent Merge。"
authors:
  - geektrainer
lastUpdated: 2026-10-22
---

筛选实现、指令更新、技能更新、质量保证（QA）配置文件和测试都已保存在同一分支。现在一起审查并创建拉取请求。你已手动合并星级评分 PR；这次将让 **Agent Merge** 管理流程。

> [!NOTE]
> 通常应将功能、指令更新、技能更新和 QA 智能体拆分到不同 PR 中。为简化研讨会，筛选和质量工作流共用一个会话和分支，并将所有更改放进同一 PR。

本课将介绍如何：

- 了解 Agent Merge 及其如何自动化合并生命周期。
- 检查完整功能 PR 和验证证据。
- 审查后才授权 Agent Merge，并确认 PR 已合并。

## 场景

在整个筛选工作流中，你使用 Copilot 规划、实现并验证了功能。现在 Tailspin Toys 希望自动处理 PR 的后续工作，同时由开发者掌控合并授权。

## 了解 Agent Merge

**Agent Merge** 可在 GitHub Copilot app 中自动处理拉取请求合并前的剩余工作。启用后，应用会话会读取 PR 并处理阻塞项，包括修复失败的持续集成（CI）检查、回复审查意见，以及在需要时变基；GitHub 允许后便会合并。它在后台运行，应用重启后仍会继续，并在 PR 合并后自动关闭。

之前由你手动选择 **Merge pull request**。Agent Merge 可以承担这项工作，但要编辑代码和合并仍需要明确授权。授予合并权限前，请审查其允许执行的操作和已完成的工作。

## 使用 Agent Merge 管理 PR

代码已创建并审查完毕，现在让 Agent Merge 管理 PR 流程。

1. 在智能体选择器中选择 **Default agent**。
2. 选择 **Create PR** 旁的下拉菜单。
3. 选择 **Agent Merge**。按钮会变为 **Agent Merge**。
4. 选择 **Agent Merge** 按钮，启动流程。只选择下拉选项并不会启动流程。

Agent Merge 流程开始运行。它会：

- 创建包含标题和说明的拉取请求。
- 如果会话从议题启动，则在 PR 描述正文中引用相关议题。
- 对目标分支执行变基或处理潜在合并冲突。
- 监控 CI 流程，确保所有检查通过。
- 监控其他开发者或 Copilot 代码审查的反馈，并进行更新以解决意见。
- 可以选择在所有操作成功后自动合并 PR。

接下来让 Agent Merge 在所有检查通过后合并 PR：

5. 授予合并权限前，审查 PR 差异和验证证据。
6. 选择 **Agent Merge** 旁的下拉菜单。
7. 确认 **Merge pull request** 旁有勾选标记。
8. 明确要求智能体合并：

   ```plaintext
   Merge the PR once all required checks and approvals pass. Do not bypass repository protections.
   ```

9. 跟踪进度并处理报告的阻塞项。继续前确认 PR 状态为 **Merged**；仅启用合并权限或智能体正在运行，不代表已经完成合并。

> [!IMPORTANT]
> Agent Merge 不会绕过存储库保护或缺失权限。继续前解决这些阻塞项。

## 总结与后续步骤

你已自动化开发流程的多个部分，包括生成代码、测试和验证代码，以及管理拉取请求。本课中，你：

- 了解了 Agent Merge 及其如何自动化合并生命周期。
- 检查了完整功能 PR 和验证证据。
- 仅在审查后授权 Agent Merge，并确认 PR 已合并。

接下来[使用现有画布并创建分类画布][next-lesson]，了解与智能体协作检查、规划和可视化工作的更多方式。

## 资源

- [使用 GitHub Copilot app 管理议题和拉取请求][managing-issues-prs]
- [关于 GitHub Copilot app][about-copilot-app]

[next-lesson]: ../9-canvases/
[managing-issues-prs]: https://docs.github.com/copilot/how-tos/github-copilot-app/managing-issues-and-pull-requests
[about-copilot-app]: https://docs.github.com/copilot/concepts/agents/github-copilot-app
