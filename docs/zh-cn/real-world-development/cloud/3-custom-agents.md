---
title: "练习 3 - 自定义智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## 什么是自定义智能体？

GitHub Copilot 中的[自定义智能体][custom-agents-concept]是针对开发工作流中特定任务或领域的专用 AI 助手。通过在存储库 `.github/agents` 文件夹中的 Markdown 文件定义智能体，可以为 Copilot 提供具体指令、最佳实践、编码模式和领域知识，帮助它更好地完成特定类型的工作。团队可以将专业知识整理成可复用智能体，例如执行 [WCAG][wcag] 标准的无障碍智能体、遵循安全编码实践的安全智能体，或维护一致测试模式的测试智能体。

自定义智能体通过项目 `.github/agents` 文件夹中的 Markdown 文件定义，也可以在 `~/.copilot/agents` 中全局定义。每个文件都包含 YAML frontmatter，至少包含 `name` 和 `description`，后面是定义智能体行为、专长和指令的 Markdown 提示。

### 自定义智能体与智能体技能

自定义智能体与[智能体技能][agent-skills-concept]有些相似：两者主要通过 Markdown 文件定义，并告诉 AI 如何执行操作。可以这样区分：**自定义智能体**是工作者，**技能**是工具。

自定义智能体有独立的上下文窗口，并可在工作中协调技能甚至其他智能体。本实验中的无障碍自定义智能体会根据无障碍准则审查并更新网站；它还可以调用 PR 工作流技能或运行和管理测试的技能。

> [!NOTE]
> 编写自定义智能体没有唯一的正确方式。与 AI 的其他用法一样，需要通过测试和迭代，找到适合环境和场景的做法。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

本课将介绍如何：

- 了解自定义智能体的定义方式。
- 将任务分配给自定义智能体。

## 场景

Tailspin Toys 致力于让所有用户都能使用其众筹平台，无论用户的视觉能力或偏好如何。近期反馈指出，一些用户认为当前深色主题难以阅读，因为文本和背景的对比度不足。设计团队因此要求添加可切换的高对比度模式。

无障碍性至关重要，因此希望尽快实现。接下来使用自定义智能体生成该功能。

## 查看无障碍自定义智能体

项目已提供无障碍自定义智能体。先查看它如何指导 Copilot：

1. 返回 Codespace 并打开 `.github/agents/accessibility.md`。
2. 查看包含 `name` 和 `description` 字段的 YAML frontmatter。

> [!CAUTION]
> 自定义智能体必须包含 `name` 和 `description` frontmatter。

3. 浏览其余部分，了解无障碍网站代码生成的核心职责、最佳实践、HTML/CSS/JavaScript 示例，以及常见问题。

## 创建并分配议题

GitHub 的 agents 页面是管理环境中所有智能体的中心位置。可以将任务分配给 Copilot 云智能体、监控任务并提供额外指导。现在将创建高对比度模式的任务交给 Copilot。

1. 打开存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue**。
5. **Title** 输入 `Add high contrast mode to website`。
6. **Description** 输入以下内容：

   ```plaintext
   We need a high contrast mode for the site. There should be a toggle for high contrast which the user can set. It should store the setting in local storage on the browser.
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。
9. 从自定义智能体列表中选择 **Accessibility agent**。

   ![云智能体分配界面中选中了 Accessibility agent](../../../_images/ex5-select-custom-agent.png)

10. 选择 **Assign**。
11. Copilot 会在后台开始处理任务。

## 总结与后续步骤

本课介绍了 GitHub Copilot 中的[自定义智能体][custom-agents]，它们是针对特定任务和领域的专用 AI 助手。团队可以将专业知识和标准整理成可复用智能体，指导 Copilot 更有效地完成工作。

本课中，你了解了：

- 自定义智能体的定义方式。
- 将任务分配给自定义智能体。

Copilot 正在实现高对比度模式。接下来从 agents 页面（任务控制中心）[监控并引导智能体会话][next-lesson]。

## 资源

- [关于自定义智能体][custom-agents]
- [在组织中使用自定义智能体前的准备工作][org-custom-agents]
- [在企业中使用自定义智能体前的准备工作][enterprise-custom-agents]

---

| [← 上一课：GitHub Copilot 云智能体][previous-lesson] | [下一课：监控和管理智能体 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../2-cloud-agent/
[next-lesson]: ../4-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[org-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents
[enterprise-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents
