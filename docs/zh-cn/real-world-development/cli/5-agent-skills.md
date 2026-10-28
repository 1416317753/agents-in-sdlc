---
title: "第 5 课 - 自定义并使用 quality-checks 技能"
description: "探索现有的 quality-checks 技能、自定义结果报告格式，并用它验证筛选功能。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

编写代码不只是写代码。我们已手动验证代码，并通过指令文件确保代码符合标准。接下来还要考虑测试、lint 和持续集成（CI）的其他部分。

这类任务适合使用**智能体技能**。技能可帮助 Copilot 正确执行这些操作。

本课将介绍如何：

- 探索现有的 `quality-checks` 技能。
- 自定义结果的报告格式。
- 重新加载并运行技能。

## 场景

Tailspin Toys 使用 `quality-checks` 技能运行单元测试、lint 和类型检查。团队希望改进报告，让结果更易于阅读。

## 指令、脚本和资源

智能体技能将可复用的任务指令、可执行脚本和支持资源打包在一起，供智能体按需加载。技能的核心是一个以技能名称命名的文件夹，其中包含 `SKILL.md` 文件。该 Markdown 文件包含定义技能名称和描述的 frontmatter、技能概述，以及说明何时调用技能的指导。文件夹还可以包含脚本和其他资源。

> [!NOTE]
> 技能不一定需要其他文件或文件夹。Tailspin Toys 的 `quality-checks` 技能只包含 `SKILL.md`，因为它使用项目已有的命令。

技能可以放在项目的 `.github/skills` 文件夹中，作为团队共享和复用的存储库资源；也可以放在用户的技能文件夹 `~/.copilot/skills` 中。

## 探索技能

查看 Tailspin Toys 团队用于运行单元测试、lint 和类型检查的 `quality-checks` 技能：

1. 返回 Codespace，在 Codespaces 编辑器中打开 `.github/skills/quality-checks/SKILL.md`。
2. 阅读顶部的 `name` 和 `description`。描述可以帮助 Copilot 判断何时调用技能。
3. 阅读指令，了解其如何引导 Copilot 执行测试和 lint。
4. 注意技能目前尚无 **Results output formatting** 部分。

## 更改前先运行技能

通过 Copilot CLI 直接调用技能，或使用自然语言请求。先让 Copilot 运行三项检查：

1. 返回筛选对话并切换到 Interactive 模式。
2. 使用以下提示：

   ```plaintext
   Run the quality-checks skill for unit tests, lint, and type checks.
   ```

3. 查看最终报告。

## 自定义报告

让报告说明运行了什么、是否成功，以及工具实际报告的内容。更新技能，生成这种报告：

1. 返回 `.github/skills/quality-checks/SKILL.md`。
2. 在文件末尾添加以下部分：

   ```markdown
   ## Results output formatting

   Upon completion, report each command that ran and whether it passed, failed, or was blocked. Include test counts, durations, errors, warnings, and other metrics only when the tool reports them. Identify the next action for any failure or blocker, and never describe a skipped or incomplete check as passed.
   ```

3. 文件会自动保存。

## 运行更新后的技能

Copilot CLI 可在对话不中断的情况下重新加载已编辑的技能：

1. 输入：

   ```plaintext
   /skills reload
   ```

2. 使用与之前完全相同的提示：

   ```plaintext
   Run the quality-checks skill for unit tests, lint, and type checks.
   ```

3. 查看最终报告，并与首次报告比较。

## 总结与后续步骤

你已自定义并使用现有技能。本课中，你：

- 探索了用于单元测试、lint 和类型检查的 `quality-checks` 技能。
- 自定义了技能的结果格式。
- 重新加载并运行了技能。

此更改会与筛选功能一起纳入功能 PR。接下来通过 [Playwright MCP 服务器][next-lesson]让 Copilot 直接与网站交互。

## 更多技能示例

以下社区示例仅供参考，并非额外任务：

- [Agent Skills 规范][skill-spec]
- [贡献工作流：`make-repo-contribution`][contribution-example]
- [需求文档：`prd`][prd-example]
- [图表和捆绑的导出脚本：`drawio`][drawio-example]
- [浏览器测试：`webapp-testing`][browser-example]

[previous-lesson]: ../4-custom-instructions/
[next-lesson]: ../6-mcp-playwright/
[skill-spec]: https://agentskills.io/specification
[contribution-example]: https://github.com/github/awesome-copilot/tree/main/skills/make-repo-contribution
[prd-example]: https://github.com/github/awesome-copilot/tree/main/skills/prd
[drawio-example]: https://github.com/github/awesome-copilot/tree/main/skills/drawio
[browser-example]: https://github.com/github/awesome-copilot/tree/main/skills/webapp-testing
