---
title: "第 6 课 - 使用 Playwright MCP 验证功能"
description: "在 Copilot CLI 中检查或配置 Playwright MCP，并用它在浏览器中体验筛选功能。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

前面提到过，编写代码不只是写代码。我们还需要处理数据、外部服务，并让 Copilot 使用更多自动化工具。MCP 服务器可以扩展 Copilot CLI 内置功能，提供更多工具和服务。

本课将介绍如何：

- 了解 Model Context Protocol (MCP) 及 Copilot CLI 对它的使用方式。
- 如果尚未添加 Playwright MCP 服务器，则进行配置。
- 让智能体控制浏览器并体验筛选功能。

## 场景

单元测试和端到端测试很重要，但验证界面更改需要实际与界面交互。允许 Copilot 像用户一样操作网站，可以进一步自动验证更改是否符合预期。

## 什么是 Model Context Protocol (MCP)？

[Model Context Protocol (MCP)][mcp-blog-post] 为 AI 智能体与外部工具和服务的通信提供方式。借助 MCP，智能体可以实时访问信息并代表你执行操作。

MCP 服务器是智能体访问这些工具和资源的桥梁。每个 MCP 服务器都提供一组可供智能体使用的工具和资源。常用服务器包括：

- **[GitHub MCP Server][github-mcp]**：提供管理 GitHub 存储库、议题和拉取请求的 API。
- **[Playwright MCP Server][playwright-mcp-server]**：使用 Playwright 提供浏览器自动化能力。

还有许多其他 MCP 服务器。GitHub 提供 [MCP 注册表][mcp-registry]，方便发现和参与 MCP 生态。

> [!CAUTION]
> 请像对待项目的其他依赖项一样对待 MCP 服务器。使用前检查源代码、验证发布者，并评估安全影响。

## 添加 Playwright MCP 服务器

如果尚未配置 Playwright MCP 服务器，请添加它，让 Copilot 可以像用户一样操作网站。

> [!NOTE]
> 如果 Copilot CLI 中已配置 Playwright MCP，请跳过这些设置步骤，继续下一节。

1. 返回 Codespace。
2. 在 Copilot CLI 中输入以下命令，打开添加 MCP 服务器的对话框：

   ```plaintext
   /mcp add
   ```

3. 名称输入 `playwright`，然后按 <kbd>Tab</kbd>。
4. 按 <kbd>Enter</kbd> 确认服务器类型为 **STDIO**，然后按 <kbd>Tab</kbd>。
5. 在 **Command** 对话框中输入：

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. 按 <kbd>Control</kbd>+<kbd>S</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>S</kbd>（Windows/Linux）保存新的 MCP 服务器。
7. 按 <kbd>Esc</kbd> 退出 MCP 对话框。

## 让 Copilot 使用 Playwright 体验功能

之前已手动确认功能符合预期。现在使用 Copilot 中的 Playwright MCP 服务器完成同样的操作：

1. 使用以下提示，让 Copilot 通过 Playwright MCP 验证功能：

   ```plaintext
   Start the app and use Playwright MCP to check filtering against the issue and our plan. Tell me what works and what doesn't, without making changes. Stop the server you started when you're done.
   ```

> [!NOTE]
> 通常 Copilot 会自动选择 MCP 服务器，不必明确要求它使用该服务器。不过，明确指出工具有助于获得更一致的结果，也可以节省一些 token。

2. 观察 Copilot 在浏览器中执行的步骤。
3. 阅读报告，确认行为符合预期。

Copilot 会启动服务器、通过 Playwright 操作网站、停止服务器并提供报告。

## 总结与后续步骤

你已通过 Copilot CLI 使用 Playwright MCP 服务器在真实浏览器中体验功能。本课中，你：

- 了解了 Model Context Protocol (MCP) 及 Copilot CLI 对它的使用方式。
- 如果尚未配置，则添加了 Playwright MCP 服务器。
- 让智能体控制浏览器并体验筛选功能。

接下来[创建 QA 自定义智能体][next-lesson]，将技能与浏览器工具结合起来。

## 资源

- [MCP 是什么，为什么大家都在讨论它？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [向 Copilot CLI 添加 MCP 服务器][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
