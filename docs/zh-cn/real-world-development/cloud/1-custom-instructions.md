---
title: "练习 1 - 自定义指令（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，或 Copilot 需要了解背景信息，就要确保它能获取这些上下文。[指令文件][instruction-files]可提供此类指导，让 Copilot 不仅了解要做什么，也了解应如何完成。

本练习将介绍如何：

- 通过存储库自定义指令和按路径限定的指令文件，向 Copilot 提供项目上下文、编码准则和文档标准。
- 向 `.github/copilot-instructions.md` 添加新的存储库级标准。

> [!NOTE]
> 与 VS Code 和 CLI 环境不同，本练习不会比较提示执行前后的结果。Copilot 云智能体通过 GitHub 议题异步工作，因此难以实时并排展示指令的影响。之后审查云智能体生成的拉取请求时，可以查看指令的影响。

## 指令文件

### 场景

Tailspin Toys 有一套开发准则：

- 数据层始终需要单元测试。
- UI 应采用深色模式并具有现代感。
- 应使用 TSDoc 文档注释记录代码。
- 每个文件开头都应添加说明文件用途的注释块。

使用指令文件可确保 Copilot 按照这些实践完成任务。

### 自定义指令

自定义指令可向 Copilot 提供上下文和偏好，帮助它理解你的编码风格和要求。可以指定编码约定、库以及注释风格等偏好。既可以创建适用于整个存储库的指令，也可以创建适用于特定文件类型或任务的指令。

有两种指令文件：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此单一文件。内容应是适用于大多数 Copilot 聊天或 CLI 请求的项目级信息。
- `.github/instructions/*.instructions.md`：为特定任务或文件类型提供指导，例如 TypeScript、Astro、UI 组件或单元测试。

> [!NOTE]
> 在 IDE 中，指令文件只用于 Copilot Chat 的代码生成，不用于代码补全或下一个编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时会使用存储库级指令，以及带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还[支持其他标准的指令文件][custom-instructions-support]，包括 AGENTS.md 和 CLAUDE.md。

### 管理指令文件的最佳实践

完整讨论指令文件创建流程超出了本研讨会的范围。示例项目展示了常见方法：

- 将 `copilot-instructions.md` 用于项目级指导，例如项目简介、结构和全局编码标准。
- 使用 `*.instructions.md` 为特定文件类型（单元测试、Astro 组件、数据层）或任务提供指导。
- 使用自然语言，保持说明清楚，并提供正反示例。

指令文件没有唯一的正确写法，AI 的使用方式也不止一种。可以通过试验找到适合项目的做法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应有一套完善的指令文件。项目中包含多种任务的指令，例如 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也能帮助生成指令文件。不同产品界面的方式有所不同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`。对应界面的课程会在相关步骤中说明。
>
> 需要模板或起点？可以查看包含指令文件、自定义智能体和其他资源的 [awesome-copilot][awesome-copilot]。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 查看项目中的自定义指令文件

查看存储库提供的核心 `copilot-instructions.md` 和各类 `*.instructions.md` 文件，可在 GitHub Web 界面或 Codespace 中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 查看项目简介以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。留意 **Code standards** 中的 **GitHub Actions Workflows** 指导；这些内容适用于与 Copilot 的所有交互。
3. 打开 `.github/instructions` 文件夹，查看 Astro、Drizzle 数据层、测试等指令。
4. 打开 `.github/instructions/unit-tests.instructions.md`。顶部的 `applyTo` 字段设置了相对于存储库根目录的 glob；例如，匹配 `**/*.test.ts` 的 TypeScript 测试文件会应用这些指令。
5. 查看该文件中针对本项目单元测试的说明。
6. 打开 `.github/instructions/drizzle.instructions.md` 并查看底部链接。它链接到其他指令文件（如 `unit-tests.instructions.md`）和项目现有文件。路径相对于指令文件，而非存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码标准，但尚未要求代码内文档。接下来会添加 TSDoc 注释和文件头注释的规则。

## 添加新的存储库标准

接下来编辑一次项目级规则，要求在代码中添加 TSDoc 注释和文件级注释。后续本环境练习中，云智能体会使用此指导。

先创建分支。云智能体会从议题目标分支读取指令，但仍可在分支上清晰提交本次更改：

1. 在 Codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

`.github/copilot-instructions.md` 用于提供项目级信息。现在添加编码标准，改善代码建议：

2. 重新打开 `.github/copilot-instructions.md`。
3. 找到大约第 27 行附近的 **Code formatting requirements** 部分。它尚未要求代码内文档，因此生成的辅助函数没有文档注释。
4. 在现有标准下方添加以下内容，要求添加文件头注释和 TSDoc 注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

5. 保存 `copilot-instructions.md`。

> [!TIP]
> 指令文件可以放在存储库级别（`.github/copilot-instructions.md`），为整个项目提供指导；也可以使用 `*.instructions.md` 文件为特定语言、文件类型或任务提供说明。刚才添加的文档注释规则属于项目级标准，因此存储库级文件是合适的位置。

## 提交、推送并合并指令更新

云智能体会读取议题目标分支中的指令。下一练习中将议题分配给 Copilot 时，它会从 `main` 创建分支。因此，必须先将指令更改合并到 `main`，云智能体才能读取。

1. 暂存并提交更改：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 合并到 `main` 的拉取请求，并合并该请求。下一练习中，云智能体便会读取这些指令。

> [!TIP]
> 如果希望在本研讨会中直接操作 `main`，可以跳过分支步骤并直接提交到 `main`。此处创建分支是为了模拟真实项目中的工作方式。

## 总结与后续步骤

你了解了 Copilot 如何读取项目指令，并向 `.github/copilot-instructions.md` 添加新的存储库级标准。后续练习中，可以在云智能体生成的 PR 中看到该标准的效果。

接下来[将第一个议题分配给 Copilot 云智能体][next-lesson]。

## 资源

- [GitHub Copilot 自定义指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义指令的 5 个提示][copilot-instructions-five-tips]
- [Awesome Copilot：指令文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
