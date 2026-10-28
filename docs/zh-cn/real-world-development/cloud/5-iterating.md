---
title: "练习 5：迭代 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作

在本实验中，你使用 GitHub Copilot 完成了多个改善用户体验和添加功能的任务：为代码添加文档、实现供设计团队迭代的相关游戏功能，以及实现高对比度和浅色模式等无障碍功能。现在审查代码更改；必要时向 Copilot 提供反馈，改进其工作。

### 场景

如前所述，引入生成式 AI 并不会改变软件设计和 DevOps 的基本原则。始终需要审查生成的代码并遵循正常的 DevOps 流程。现在先审查 GitHub Copilot 为文档、相关游戏功能和无障碍功能提出的更改，再开放团队审查。

## GitHub Copilot 云智能体的安全机制

Copilot 云智能体会异步且自主地执行任务，因此设置了安全限制：

- Copilot 只有存储库读取权限，并且仅对它用于代码更改的分支具有写入权限。
- 云智能体在 GitHub Actions 中运行，并创建独立的临时环境。
- 运行任何 GitHub Actions 工作流前，都需要人工批准。
- [默认情况下，外部资源访问受限](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，包括 MCP 服务器。

## 审查生成的文档

先查看 GitHub Copilot 云智能体创建的第一个 PR：为代码添加文档。通过 GitHub.com 上的标准 PR 界面完成审查。

> [!NOTE]
> PR 页面可能会显示 GitHub Copilot 被防火墙阻止的警告。这是预期行为，因为 Copilot 默认只能有限访问外部资源，包括外部 MCP 服务器。可以[自定义或禁用 Copilot 云智能体防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开 PR 列表。
3. 打开标题类似 **Add missing documentation** 或内容更具体的 PR。

> [!NOTE]
> 如果 Copilot 尚未完成任务，PR 标题会带有 **[WIP]** 标记。请等待 Copilot 完成；这可能需要几分钟，可以利用这段时间休息或回顾所学内容。

4. PR 准备好后，选择 **Files changed** 选项卡并审查更改。

   ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 检查新增的 TSDoc 文档注释和其他文档。具体更改可能不同。审查时，检查 TSDoc 注释、TypeScript 约定和文件头注释。这些要求来自练习 2 开始时查看的自定义指令文件。
6. 确认更改无误后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有等待批准的工作流。
8. 如果有，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 工作流会出现在 PR 的检查区域中。正常情况下，单个 Astro 应用的项目检查将通过。此过程可能需要几分钟。

## 向 GitHub Copilot 请求更改

PR 协作并非单向过程。你可以像提及团队成员一样在 PR 评论或代码内联评论中提及 Copilot。Copilot 会读取评论并启动新会话处理请求。由于结果具有概率性，无法指定固定文本；可以请求 Copilot：

- 在每个代码文件顶部添加简要说明文件用途的注释块。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建 README，介绍 Astro 应用结构。

1. 添加评论请求 Copilot 修改生成的文档，并像提及用户一样标记 **@copilot**。可以使用上述建议，或提出其他代码文档改进意见。
2. 选择 **View Session**，观察 Copilot 如何启动新会话并进行更改。选择 **Back to pull request** 可返回 PR。

   ![Back to pull request](../../../_images/ex7-back-to-pr.png)

3. Copilot 完成更改后，PR 中会出现新提交。
4. 选择 **Files changed** 选项卡审查更改。

可以继续迭代，直到满意为止。完成后，可将 PR 从草稿转换为准备审查状态，再合并到主分支。

![将 PR 转为准备审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

返回 Copilot 为“在游戏详情页显示相关游戏”议题创建的 PR：

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 打开标题类似 **Show related games on the game details page** 的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 确认更改无误后，返回 **Conversation** 选项卡并向下滚动。
6. 查看是否有等待批准的工作流。
7. 如果有，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. 工作流会出现在 PR 的检查区域中。正常情况下，单个 Astro 应用的项目检查将通过，此过程可能需要几分钟。
9. **可选：** 可以在 Codespace 中切换到该分支，手动测试相关游戏功能。打开 Codespace 终端并运行以下命令；将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**：

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Copilot 已构建相关游戏功能。与之前一样，可以通过新评论请求云智能体进一步修改，例如调整显示的相关游戏数量，或添加文件头注释和 TSDoc 文档注释（注意：该任务在更新自定义指令**之前**分配）。在 **Conversation** 选项卡中添加评论后，Copilot 会读取评论并启动新会话。

## 审查无障碍功能

最后审查使用无障碍自定义智能体实现的功能。此 PR 应同时包含练习 3 中分配的高对比度模式，以及练习 4 在任务控制中心中提出的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 打开标题类似 **Add high contrast mode to website** 的 PR。

> [!NOTE]
> 如果 Copilot 尚未完成任务，PR 标题会带有 **[WIP]** 标记。请等待它完成，这可能需要几分钟。

4. 选择 **Files changed** 选项卡审查生成的代码。
5. 检查实现，重点关注：
   - 用于切换模式的 UI 组件。
   - 用于保存用户偏好的本地存储。
   - 高对比度和浅色模式的 CSS 或样式更改。
   - 无障碍属性（ARIA 标签、键盘导航等）。
   - 管理模式切换的 JavaScript/TypeScript 代码。
6. 确认更改无误后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有等待批准的工作流。
8. 如果有，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 工作流会出现在 PR 的检查区域中。正常情况下，单个 Astro 应用的项目检查将通过，此过程可能需要几分钟。
10. **可选：** 可以在 Codespace 中切换到该分支，手动测试无障碍功能。打开终端并运行以下命令，将 `<branch-name>` 替换为 Copilot 创建的分支名称：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用，在浏览器中测试高对比度和浅色模式切换，并确认设置会在页面重新加载后保留。

无障碍自定义智能体帮助 Copilot 按照无障碍最佳实践实现功能。如果发现无障碍问题或改进点，可以像处理其他 PR 一样在评论中标记 **@copilot** 请求更新。

## 可选练习：继续委派任务

云智能体适合处理待办事项，让你能转而完成其他工作。可以在自己的存储库中创建更多议题并分配给 Copilot，以培养这种工作方式。例如：

- 在游戏详情页创建支持者兴趣表单。
- 在游戏列表页实现分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成云智能体环境的练习。本系列练习中，你：

- **检查了存储库附带的自定义指令文件**，以便之后对照云智能体的输出查看其影响。
- **将议题分配给 Copilot 云智能体**，观察它设置环境、制定计划并异步执行。
- **创建并使用自定义智能体**处理无障碍功能，添加高对比度和浅色模式切换开关。
- **使用 Copilot agents 页面作为任务控制中心**，监控并引导无障碍会话。
- **审查并迭代云智能体创建的 PR**，通过标记 `@copilot` 请求更改并批准工作流。

## 回顾与后续步骤

你已完成云智能体环境的练习。还可以继续学习其他环境：

- 🖥️ **[VS Code 环境](../../vscode/)**：直接在 IDE 中探索 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 环境](../../cli/)**：在终端完成两个经过审查的更改，体验 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体连接。

也可以在自己的存储库中尝试以下内容：

- 将重构或测试覆盖率议题分配给云智能体。
- 为其他领域创建自定义智能体。
- 为其他技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
