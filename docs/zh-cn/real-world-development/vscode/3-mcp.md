---
title: "练习 3 - 使用 Playwright MCP 服务器测试功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：智能体模式][previous-lesson] · [下一课：自定义智能体 →][next-lesson]

你刚刚使用智能体模式构建了筛选功能。打开拉取请求前，应先确认该功能在浏览器中正常运行。接下来连接 **Playwright MCP 服务器**，让 Copilot 控制真实浏览器测试功能，然后发布分支并创建 PR。

本练习将介绍如何：

- 了解 Model Context Protocol (MCP) 以及 MCP 服务器如何扩展 Copilot 工具。
- 将 Playwright MCP 服务器添加到工作区。
- 让 Copilot 使用该服务器在浏览器中手动测试筛选功能。
- 发布分支并为筛选工作创建拉取请求。

## 什么是 Model Context Protocol (MCP)？

智能体模式可以访问编辑器之外的资源，从而发挥更大作用。Model Context Protocol (MCP) 是 Copilot 与外部工具和服务通信的标准方式。

![智能体模式的工作原理示意图，展示上下文、大语言模型和工具，以及 MCP 服务器和 VS Code 扩展提供的工具](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) 让 AI 智能体能够与外部工具和服务通信。智能体可以通过资源获取最新信息，并使用工具代表你执行操作。

MCP 服务器是智能体与外部工具及服务之间的桥梁，负责管理双方通信。外部工具可以是现有 API，也可以是 NPM 包等本地工具。每个 MCP 服务器提供一组可供智能体使用的工具和资源。

常见服务器包括：

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**：提供管理 GitHub 存储库的 API，使智能体能够创建存储库、更新存储库以及管理议题和拉取请求。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**：使用 Playwright 提供浏览器自动化能力，使智能体能够浏览网页、填写表单和选择按钮。

还有许多其他 MCP 服务器提供不同工具和资源。GitHub 提供 [MCP 注册表](https://github.com/mcp)，方便发现和参与 MCP 生态。

> [!CAUTION]
> 请像对待项目的其他依赖项一样对待 MCP 服务器。使用前仔细检查源代码、验证发布者并评估安全影响。只使用信任的 MCP 服务器，并谨慎授予敏感资源或操作的访问权限。

## 查看 MCP 配置

`.vscode/mcp.json` 文件配置了 VS Code 工作区可用的 MCP 服务器。

1. 在 Codespace 中打开 `.vscode/mcp.json`。
2. 文件中应已有 `github` 服务器：

   ```json
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       }
     }
   }
   ```

此 `github` 项来自项目模板，因此 Copilot 在上一练习中可以读取待办议题。它使用[远程 GitHub MCP 服务器][remote-github-mcp-server]，无需本地安装；VS Code 通过 HTTP 连接，并在 Copilot 首次使用工具时使用 GitHub 身份验证。

## 添加 Playwright MCP 服务器

接下来添加第二个服务器。[Playwright MCP 服务器][playwright-mcp-server]可为 Copilot 提供可控的浏览器，用于测试功能。

1. 在 `.vscode/mcp.json` 中，在 `github` 旁添加 `playwright` 项：

   ```json
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       },
       "playwright": {
         "command": "npx",
         "args": ["@playwright/mcp@latest", "--headless"]
       }
     }
   }
   ```

2. 保存文件。

与远程 `github` 服务器不同，`playwright` 是**本地**服务器：VS Code 会运行 `npx @playwright/mcp@latest` 启动它。`--headless` 参数可让 Playwright 在没有可见窗口的情况下运行浏览器，这适用于没有桌面环境的 Codespace。

> [!NOTE]
> Tailspin Toys 项目已使用 Playwright 运行端到端测试，因此通常已安装 Playwright 所需的浏览器。如果 Copilot 报告缺少浏览器，请让它运行 `npx playwright install chromium` 后重试。

## 启动并信任服务器

VS Code 会按需启动 MCP 服务器：Copilot 首次需要服务器工具时，VS Code 会启动服务器，并询问是否信任它。

1. `.vscode/mcp.json` 中的 `playwright` 项上方会出现 **Start** 操作。选择它，立即启动服务器并确认配置有效。
2. 如果 VS Code 询问是否信任服务器，请审查配置并选择信任，以便启动服务器。

手动启动是可选步骤；如果跳过，VS Code 会在下一步首次需要服务器工具时启动服务器并询问是否信任。

## 测试筛选功能

[Playwright MCP 服务器][playwright-mcp-server]为 Copilot 提供可控的真实浏览器。智能体可以打开页面、导航、应用筛选并读取结果，然后向你总结观察到的行为，无需离开对话即可快速验证功能。

Playwright MCP 服务器在底层使用页面的[无障碍树][playwright-mcp-server]而非屏幕截图。智能体根据结构化且带标签的元素（按钮、链接、列表项）进行推理，与辅助技术的工作方式相同，因此功能检查也能顺便进行初步无障碍检查。

连接服务器并启动应用后，使用以下提示让 Copilot 测试筛选功能：

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot 会通过 Playwright MCP 服务器启动浏览器、完成各项操作并报告结果。根据议题的验收标准审查报告；如果发现异常，请继续询问，或让 Copilot 修复代码后再创建 PR。

> [!NOTE]
> 测试时应用必须运行在 `http://localhost:4321`。如果开发服务器已停止，请先重新启动。Copilot 首次使用 Playwright MCP 服务器时可能需要下载浏览器；如果报告缺少浏览器，请让它运行 `npx playwright install chromium` 后重试。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## 发布分支并创建拉取请求

确认功能正常后，准备创建拉取请求（PR），让团队审查。先发布 `filtering-vscode` 分支：

1. 在 Codespace 中打开 **Source Control** 面板，并审查 Copilot 所做更改。
2. 选择 **+** 暂存更改。
3. 使用 **Sparkle** 按钮生成提交消息。

   ![显示更改内容的 Source Control 面板](../../../_images/ex3-source-control-changes.png)

4. 选择 **Publish**，将分支推送到存储库。

## 创建拉取请求

可以通过 github.com 或 GitHub 命令行界面（CLI）创建 PR。也可以让 Copilot 使用已连接的 GitHub 工具查找相关议题并创建关联的 PR。

1. 打开 Copilot Chat 面板，选择 **New Chat** 开始新会话。
2. 在智能体下拉列表中选择 **Agent**，以便 Copilot 使用 GitHub 工具。
3. 使用以下提示创建 PR：

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 根据需要选择 **Continue**，允许 Copilot 获取信息并执行操作。首次使用 GitHub 工具时，可能会提示通过 GitHub 身份验证；按提示授权即可。
5. Copilot 会搜索议题、找到相关议题并创建 PR。
6. 选择 Copilot 提供的链接审查 PR，但**暂时不要合并**。

## 总结与后续步骤

本练习中，你：

- 了解了 Model Context Protocol (MCP) 以及 MCP 服务器如何扩展 Copilot 工具。
- 将 Playwright MCP 服务器添加到工作区。
- 在发布前使用它在浏览器中测试筛选功能。
- 发布分支并为筛选工作创建了拉取请求。

你使用 MCP 服务器测试了功能，但 MCP 只是扩展 Copilot 的一种方式。接下来创建[自定义智能体][next-lesson]，简化无障碍审查等专门任务。

## 资源

- [MCP 是什么，为什么大家都在讨论它？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP 注册表][mcp-registry]
- [VS Code 中的 MCP 服务器][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
