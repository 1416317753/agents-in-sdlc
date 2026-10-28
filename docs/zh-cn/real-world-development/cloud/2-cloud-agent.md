---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义指令][previous-lesson] |
|:--|

许多组织都面临技术债务，例如未解决的安全问题、需要更新的旧代码，或因缺少时间而长期积压的功能请求。GitHub Copilot 云智能体可自主更新代码和添加功能。完成任务后，它会创建草稿 PR，供开发者审查。这样可以将繁琐任务交给智能体，加快开发流程，让开发者专注于更重要的工作。

本课将介绍如何使用 Copilot 云智能体：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 编写范围明确的议题。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债务要处理。最初为网站编写代码的承包商没有提供足够文档。团队希望首先为应用中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队想改善游戏发现体验：在每款游戏的详情页显示同类别的其他游戏，方便用户继续浏览。团队暂时不需要完善的实现，只需要可用于 UX 验收测试的版本。目前这项工作被其他更高优先级的议题阻塞。

这类任务很容易被不断延后，适合交给 Copilot 云智能体异步处理。开发者可先处理其他工作，之后再回来审查 Copilot 的成果。

## 了解 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像开发者一样在后台执行任务。可以通过多种方式启动工作，例如[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求跟踪进度、设置环境并开始工作。会话进行期间或完成后都可以查看进度。解决方案准备好审查时，Copilot 会在拉取请求中提及你。

## 明确任务范围的重要性

GitHub Copilot 没有魔法，不能替代思考。即使看似简单的操作，也可能包含复杂环节，因此要谨慎设定云智能体的任务范围，并遵循[云智能体最佳实践][cloud-agent-best-practices]。将它视作 AI 结对程序员：分阶段工作、学习、试验并不断调整。引入生成式 AI 不会改变软件开发的基本原则。

## 此存储库中的自定义指令

前面的练习介绍了自定义指令及其作用。如果已完成自定义指令练习，就已看到 `.github/copilot-instructions.md` 的效果；否则可先快速查看。分配任务前，以只读方式检查存储库中的指令文件，稍后可对照查看其影响：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，尤其是 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：留意 `applyTo` frontmatter，它将指令限定在 `**/*.test.ts` 文件。

下一节将 `Code lacks documentation` 议题分配给云智能体时，注意查看其 PR 中的 TSDoc 文档注释、TypeScript 约定和文件头注释。这些内容来自上述指令文件，后续练习会再次检查。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要准备特定环境并运行设置脚本，确保环境就绪。Copilot 的工作方式类似软件工程师，因此也需要环境准备。

云智能体使用 [GitHub Actions][github-actions] 执行任务。可以通过专用设置工作流自定义环境：在 `.github/workflows/copilot-setup-steps.yml` 中配置[设置工作流][setup-workflow]，在 Copilot 开始工作前安装所需工具和依赖项。本实验已提前配置该工作流，确保 Copilot 可以使用 Node.js、项目依赖项、端到端测试所需的浏览器，以及迁移并填充数据的 SQLite 数据库（该项目是单个 Astro 应用）：

```yaml
name: "Copilot Setup Steps"

# 可在存储库的“Actions”选项卡中手动测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 将权限限制为步骤所需的最低级别。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 如果要在设置步骤中克隆存储库（例如安装依赖项），需要 `contents: read` 权限。如果设置步骤不克隆存储库，步骤完成后 Copilot 会自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端/应用设置 - Node.js（整个应用现为 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，为构建和测试准备数据。
      - name: Set up the database
        run: npm run db:setup
```

此文件与其他 GitHub 工作流类似，但有几点值得留意：

- 它只包含名为 `copilot-setup-steps` 的作业。Copilot 开始处理 PR 前，GitHub Actions 会运行此作业。
- `workflow_dispatch` 触发器可让你在存储库的 **Actions** 选项卡中手动运行工作流。这样可以先验证工作流，而不必等待 Copilot 启动。

## 添加文档

大多数项目都存在文档过时或缺失的问题。这类技术债务往往无人处理，会拖慢开发、增加维护难度，也使新成员更难加入。Copilot 擅长生成文档，因此很适合处理此类议题。它会在后台生成文档，之后可再审查结果。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue**。
5. **Title** 输入 `Code lacks documentation`。
6. **Description** 输入以下内容：

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开新生成的 PR，标题可能类似 `[WIP]: Code lacks documentation`。如果列表中尚未显示新 PR，请稍等片刻并刷新浏览器。
12. 几分钟后，Copilot 应会生成待办清单。

> [!NOTE]
> Copilot 可能需要几分钟才能在 PR 中显示待办清单。它正在创建环境（运行前面介绍的工作流）、分析项目并确定处理任务的方案。

13. 审查任务清单和 Copilot 即将执行的工作。
14. 向下滚动 PR 时间线，确认 Copilot 已开始处理议题。
15. 选择 **View session** 按钮。

   ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新页面才能看到更新后的状态。

16. 查看实时会话，了解 Copilot 如何探索代码、分析现状、暂停思考并制定计划，然后生成代码。

此过程可能需要几分钟。云智能体的主要优势之一是异步执行任务，让你可以处理其他工作。接下来利用这一点：再分配一项任务，然后转而为应用编写其他功能。

## 为游戏详情页添加相关游戏

GitHub Copilot 云智能体的一大优点是可以拆分工作：你处理一项任务，云智能体处理另一项任务。即使添加相关游戏功能不需要太多时间，将其交给云智能体也能腾出时间处理其他工作。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue**。
5. **Title** 输入 `Show related games on the game details page`。
6. **Description** 输入以下内容：

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

不久后，议题的第一条评论中会显示 👀，表示 Copilot 已开始处理。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在处理第二项请求。云智能体的工作方式类似软件工程师，因此无需一直监控，完成后再审查即可。接下来创建并使用自定义智能体。

## 总结与后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。可以将议题分配给 Copilot，让它异步处理技术债务、开发功能或迁移框架等任务。

本课中，你了解了：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 编写范围明确的议题。
- 将议题分配给 Copilot。

云智能体正在后台工作，现在可以开始创建和使用自定义智能体。[Copilot 云智能体也支持 MCP 服务器][cloud-agent-mcp]，并可使用前面模块介绍的自定义指令。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
