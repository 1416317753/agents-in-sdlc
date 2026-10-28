---
title: "练习 2 - 使用 Copilot 智能体模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是简单的应用更新，通常也需要修改多个文件并运行测试。开发者通常需要找到所有相关文件、逐一修改、运行测试、调试、查找遗漏文件，再继续修改。

Copilot 智能体模式可以更自主地在 IDE 中工作。它会先探索项目结构、执行必要更改、运行测试等任务，并自动修复发现的问题。接下来使用智能体模式为网站添加新功能。

> [!NOTE]
> 智能体模式和云智能体名称相似，但适用于不同场景。智能体模式在 IDE 中执行任务，便于快速反馈和交互；云智能体则像团队同事一样异步工作，通过议题和拉取请求与你协作。

本练习将介绍如何：

- 让 Copilot 智能体模式探索项目、定位相关文件并协调更改。
- 使用 Copilot 智能体模式跨 UI 和数据层实现功能。
- 在将代码合并到代码库前审查 Copilot 生成的更改和测试。

## 场景

游戏列表越来越长，需要允许用户按类别和发行商筛选。上一练习已添加发行商辅助函数；现在使用 Copilot 智能体模式完成剩余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

先了解 Tailspin Toys 网站当前的功能，再进行修改。

该网站是面向开发者主题的棋盘游戏众筹平台，可列出游戏并显示其详情。它是一个 Astro 应用，在构建时将页面渲染为静态 HTML。页面通过 Drizzle ORM 在 frontmatter 中直接查询本地 SQLite 数据库；没有独立后端 API 或客户端 UI 框架。可复用的数据访问辅助函数放在 `src/lib/` 中，交互功能由使用标准 DOM API 的小型 Astro `<script>` 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器：

1. 返回 Codespace。上一练习后应已位于 `main`。
2. 按 <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux）打开 Codespace 终端。
3. 更新代码并为筛选工作创建新分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下的 URL：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果出现询问是否在浏览器窗口中打开 `http://localhost:4321` 的对话框，请选择 **x** 将其关闭。

5. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），并选择终端中的 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 Codespace 中选择 localhost 链接时，会自动重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是指向 Codespace 的私有隧道，网站服务器正在其中运行。

### 浏览网站

网站运行后，可以查看其主要功能：

- **主页**：列出棋盘游戏的标题、图片和描述。
- **游戏详情页**：选择游戏后查看标题、描述、发行商和类别等详细信息。

## 使用 Copilot 查看待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，以启动全新对话。模式和模型选择因练习而异；相关步骤会在需要时说明。

网站的初始实现已可正常使用，但还需要新增功能。先查看从模板创建项目时自动生成的待办议题，让 GitHub Copilot 帮助梳理优先级：

1. 在 Chat 视图的智能体下拉列表中选择 **Agent**。**Agent** 智能体会自主规划并跨文件实现更改、运行终端命令并调用工具。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者并不偏好某个模型。编写研讨会时使用了 Claude Sonnet 4.5，希望让代码建议相对一致。由于大型语言模型具有概率性，收到的建议可能与文档不同，这是正常且预期的情况。

> [!NOTE]
> Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 发送以下提示，让 Copilot 查看议题待办事项：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue**，运行列出所有议题的命令。
5. 查看生成的议题列表。

请注意，Copilot 会根据对用户的帮助程度为议题排序。

## 审查指令文件

让智能体生成代码前，先查看用于提供工作上下文的指令文件。项目中的 [UI 指令文件](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)说明了如何为网站添加功能。

1. 在 Codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看整体指导，包括：
   - 架构概述。
   - 组件设计、可测试性和无障碍原则。
   - Astro、Tailwind CSS 和 Drizzle 等文件类型的指令链接。

> [!TIP]
> 指令文件可以引用其他指令文件和项目文件。路径相对于指令文件所在位置。这样可以复用指令、将大型指令集拆分成更易管理的小文件，并为 Copilot 提供示例和模板。

## 实现筛选功能

完成筛选至少需要对应用进行三项更改：

- 在数据层（`src/lib/`）添加或完善筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页面，添加筛选 UI。

合并前还需要运行并通过测试。Copilot 智能体模式可以执行这些任务：

1. 继续当前 Copilot 对话，或选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions** 和 **ui** 指令文件。

   ![选择 UI 指令文件的示例](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉列表中仍选中 **Agent**。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

4. 确认模型仍为 **Claude Sonnet 4.5**。
5. 使用与待办议题相关的提示请求 Copilot 实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 探索项目并定位相关数据层辅助函数、UI 和测试，然后修改文件并运行测试。

   ![Copilot 探索项目文件](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会探索项目、修改文件并运行测试，可能需要几分钟。过程中编辑器中可能会出现 **Keep** 和 **Undo** 按钮。工作完成后，所有更改都会显示相应按钮，无需在工作进行期间操作。

7. Copilot 提示运行测试时，选择 **Continue**。

   ![Copilot Chat 窗格中询问是否可以运行测试的对话框](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会暂停，或看到测试暂时失败。这是正常的；Copilot 会在生成代码和运行测试之间迭代，直到任务完成且未发现错误。
9. 检查生成的代码是否存在潜在问题。

   ![Copilot 智能体模式完整会话](../../../_images/ex3-agent-mode-proposed-changes.png)

> [!CAUTION]
> 始终审查 Copilot 或其他 AI 工具生成的代码。

10. 返回运行着网站的浏览器，体验新功能。
11. 确认功能正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结与后续步骤

本练习介绍了如何使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加功能。本练习中，你了解了如何：

- 使用智能体模式跨 UI 和数据层实现功能。
- 让 Copilot 探索项目、识别相关文件并协调更改。
- 在将更改合并到代码库前审查生成的更改和测试。

接下来使用 [Playwright MCP 服务器测试功能][next-lesson]，并为其创建拉取请求。

### 额外练习：实现分页

游戏列表不断增长，需要添加分页。运用本练习中学到的技能，让 Copilot 更新网站以实现分页。考虑以下事项：

- 遵循现有最佳实践，包括使用已有指令文件。
- 决定分页方式，例如是否允许用户选择页面大小，或将页面大小固定。
- 编写提示时提供足够指导，确保 Copilot 按照预期实现功能。
- 可能需要多次迭代，请 Copilot 修改并补充上下文。这是使用 Copilot 的正常流程。

## 资源

- [Copilot 的 ask、edit 和 agent 模式：功能及适用场景][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
