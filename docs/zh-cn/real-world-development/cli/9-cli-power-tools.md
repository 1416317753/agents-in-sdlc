---
title: "第 9 课 - GitHub Copilot CLI 中的斜杠命令"
description: "探索管理上下文、选择模型、共享会话和可选云端委派的斜杠命令。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

与其他 CLI 工具一样，GitHub Copilot CLI 提供许多斜杠命令。这些命令可用于高级功能、查看后台信息和设置配置选项。你已经使用过 `/diff`、`/mcp`、`/skills`、`/agent` 和 `/pr`。接下来看看其他实用命令。

本课将介绍如何：

- 使用 `/context` 和 `/compact` 了解 Copilot 如何管理对话上下文。
- 使用 `/model` 查看可用模型。
- 了解如何使用 `/share` 导出或共享会话。
- 探索并行工作、工作树以及委派给云智能体的可选命令。

## 场景

核心 CLI 工作流已完成。现在看看其他功能：管理上下文、切换模型、共享会话，以及可选地将工作委派给 [Copilot 云智能体][about-cloud-agent]。

## 查看 Copilot CLI 上下文

处理大型或复杂任务时，可能会接近模型的最大上下文窗口。Copilot CLI 会在需要时自动压缩对话；你也可以使用斜杠命令检查或压缩上下文。

1. 返回 Codespace；如果 Copilot CLI 尚未打开，请从存储库根目录启动。
2. 输入：

   ```plaintext
   /context
   ```

3. 查看模型、当前 token 用量，以及系统指令、工具、消息和可用空间的分配情况。
4. 压缩对话：

   ```plaintext
   /compact
   ```

5. 再次输入 `/context` 并比较结果。如果对话本来就很短，变化可能不明显。

> [!NOTE]
> 上下文窗口接近上限时，Copilot CLI 会自动压缩。想自行决定压缩时机时，可使用 `/compact`。如果要切换到无关任务并开始全新对话，可使用 `/clear` 或 `/new`。

## 选择模型

不同模型各有优势，开发者的偏好也不同。Copilot CLI 可列出并选择模型。

1. 输入：

   ```plaintext
   /model
   ```

2. 查看可用模型和用量信息。
3. 保留当前模型、选择其他模型，或按 <kbd>Esc</kbd> 关闭列表。

## 共享会话

团队合作和分享经验有助于提升每个人对 AI 工具的使用水平。`/share` 命令可以将会话导出为 Markdown 或 HTML 文件、创建可共享链接，或发布为 GitHub gist。

1. 输入 `/help`，查看当前安装版本中 `/share` 的选项。
2. 如果要共享此会话，请选择合适的目标，例如使用 `/share file` 导出本地 Markdown 文件。
3. 发送或发布前审查导出内容。会话导出可能包含提示、回答和项目详情。

发布链接或 gist 是可选的。不要发布团队不打算共享的存储库或对话内容。

## 可选：扩展或委派工作

核心研讨会已完成。Copilot CLI 还为大型任务提供以下命令：

- `/fleet` 可将彼此独立的子任务分配给子智能体并行执行。
- `/worktree` 可为单独任务创建隔离的 Git 工作树。
- `/delegate` 可将任务交给 Copilot 云智能体异步处理，它可能会创建拉取请求。

这些命令是可选的，因为它们可能创建额外工作树或远程工作。尝试前，请开始一个范围明确的新任务，并按常规流程审查结果。要进一步了解异步智能体工作，请继续学习[云智能体研讨会][cloud-workshop]。

## 总结与后续步骤

Copilot CLI 斜杠命令可用于配置 CLI、共享会话并查看后台状态。本课中，你：

- 使用 `/context` 和 `/compact` 了解 Copilot 如何管理对话上下文。
- 使用 `/model` 查看可用模型。
- 了解如何使用 `/share` 导出或共享会话。
- 探索了并行工作、工作树以及委派给云智能体的可选命令。

Copilot CLI 还有更多命令值得探索。接下来[回顾所学内容和后续步骤][next-lesson]，结束本次学习。

## 资源

- [Copilot CLI 命令参考][cli-reference]
- [Copilot CLI 中的上下文管理][context-management]
- [关于 Copilot 云智能体][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
