---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前两节练习中，你让 Copilot 云智能体处理了三项改善用户体验和添加功能的任务。云智能体可异步自主运行，但仍需监控其任务。

GitHub 提供多种管理分配给云智能体任务的工具，包括 [GitHub.com 上的 agents 页面][agents-page]。在这个任务控制中心中，可以查看所有带有未关闭 PR 的智能体任务、检查执行过程，并引导正在运行的会话。

本课将介绍如何：

- 使用 agents 页面监控云智能体任务。
- 在会话进行期间引导智能体添加功能。

## 场景

将任务分配给智能体以实现高对比度模式后，团队意识到也应添加浅色模式。由于智能体已经在修改网站样式并添加切换功能，可以引导它同时实现高对比度和浅色模式。

## 查看 Copilot 云智能体任务

查看当前分配给 Copilot 云智能体的任务状态：

1. 打开 [Copilot agents 页面](https://github.com/copilot/agents)。
2. 查看主区域和左侧区域中的任务列表。应能看到已分配给 Copilot 的任务，包括：
   - 为代码库补充文档。
   - 生成用于修改游戏的 API。
   - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 已执行的工作，例如：
   - 从存储库检出代码。
   - 创建 Copilot 工作环境。
   - 设置 MCP 服务器。
   - 执行完成任务所需的其他步骤。

> [!NOTE]
> 列出的具体步骤会根据 Copilot 的当前状态和实现方式而变化。

4. 同时查看右侧的拉取请求窗格，其中显示 PR 和更改的文件，方便监控。

## 引导云智能体

查看当前任务后，在 Copilot 处理高对比度模式时要求它也添加浅色模式切换开关。

1. 选择与高对比度模式相关的会话。会话标题取决于 Copilot 使用的名称和当前工作状态。

   ![任务控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到 Copilot 完成设置并开始工作。此时应能看到类似示例中的消息。
3. 在 **Steer active session while Copilot is working** 对话框中输入以下提示：

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![Copilot agents 页面中的任务及已突出显示的引导活动会话对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 查看 Copilot 如何确认提示并将其纳入工作流程。

## 让 Copilot 继续工作

Copilot 会继续处理更新后的任务，并在完成发送提示时正在执行的具体步骤后，将新要求纳入工作流程。

此过程可能需要几分钟。等待期间，可以回顾目前学到的内容。

## 总结与后续步骤

本课介绍了 Copilot agents 页面，这是监控和引导 GitHub Copilot 云智能体任务的中心位置。通过任务控制中心，可以跟踪所有活动和已完成任务、审查正在进行的工作，并调整会话范围或提供额外指导。

本课中，你：

- 使用任务控制中心和 agents 页面监控了云智能体任务。
- 引导正在运行的会话添加功能。

Copilot 正在完成无障碍功能，接下来审查其创建的[拉取请求并进行迭代][next-lesson]。

## 资源

- [Copilot agents 页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
