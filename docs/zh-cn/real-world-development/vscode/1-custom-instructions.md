---
title: "练习 1 - 自定义指令（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，或 Copilot 需要了解背景信息，就要确保它能获取这些上下文。可以通过多种方式向 Copilot 提供具体上下文，其中一种重要方式是使用[指令文件][instruction-files]指导代码生成。

本练习将介绍如何：

- 通过存储库自定义指令和按路径限定的指令文件，向 Copilot 提供项目上下文、编码准则和文档标准。
- 在现有指令下生成筛选功能的第一个数据切片（发行商辅助函数）。
- 向 `.github/copilot-instructions.md` 添加新的存储库级标准。
- 使用相同提示重新生成代码，观察新标准的影响。
- 将指令更新和筛选数据切片提交到 `main`，供下一练习使用。

> [!CAUTION]
> 生成的代码可能不完全遵循设定的标准。Copilot 具有概率性；本练习的重点是观察更新指令前后行为的趋势，而非逐字匹配输出。

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

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此单一文件。内容应是适用于大多数 Copilot 聊天或 CLI 请求的项目级信息，例如技术栈、项目概述、最佳实践和全局指导。
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

查看存储库提供的核心 `copilot-instructions.md` 和各类 `*.instructions.md` 文件，可在编辑器或 GitHub Web 界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 查看项目简介，以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。留意嵌套在 **Code standards** 下的 **GitHub Actions Workflows** 指导；这些内容适用于与 Copilot 的所有交互。
3. 打开 `.github/instructions` 文件夹，查看 Astro、Drizzle 数据层、测试等指令。
4. 打开 `.github/instructions/unit-tests.instructions.md`。顶部的 `applyTo` 字段设置了相对于存储库根目录的 glob，决定适用文件范围。例如，匹配 `**/*.test.ts` 的 TypeScript 测试文件会应用这些指令。
5. 查看该文件中针对本项目单元测试的说明。
6. 打开 `.github/instructions/drizzle.instructions.md` 并查看底部链接。它链接到其他指令文件（如 `unit-tests.instructions.md`）和项目现有文件。路径相对于指令文件，而非存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码标准，但尚未要求代码内文档。接下来会添加 TSDoc 注释和文件头注释的规则。

## 为更改创建分支

遵循最佳实践，为更改创建分支：

1. 返回上一练习使用的 Codespace。
2. 按 <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux）打开终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新指令前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，以启动全新对话。模式和模型选择因练习而异；相关步骤会在需要时说明。

先在当前指令文件下发送提示，观察自定义指令的效果。之后更新指令，并重新发送相同提示进行比较。

1. 关闭之前练习留下的编辑器选项卡，让 Copilot 只获取需要的上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道辅助函数应放在哪里。
3. 在 Chat 视图的智能体下拉列表中选择 **Agent**，以便 Copilot 修改文件。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot 会探索项目并应用代码更改，通常会同时修改辅助函数及其测试。
6. 注意生成的辅助函数是类型化函数：它接受 `db` 客户端作为第一个参数，并返回类型化的发行商数组。这来自适用于 `src/lib/*.ts` 的 `.github/instructions/drizzle.instructions.md` 数据层约定。
7. 注意生成的代码**缺少** TSDoc 注释和文件级注释块。

> [!CAUTION]
> Copilot 具有概率性，因此它也可能在未被要求时添加文档注释。这并不影响练习目的；重点是观察指令更新后行为是否更加一致。

## 添加新的存储库标准

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在添加文档标准，改善代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到大约第 27 行附近的 **Code formatting requirements** 部分。该部分记录了编码标准，但尚无代码内文档要求，因此之前生成的辅助函数没有文档注释。
3. 在现有标准下方添加以下 Markdown 内容，要求添加文件头注释和 TSDoc 注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 指令文件可以放在存储库级别（`.github/copilot-instructions.md`），为整个项目提供指导；也可以使用 `*.instructions.md` 文件为特定语言、文件类型或任务提供说明。刚才添加的文档注释规则属于项目级标准，因此存储库级文件是合适的位置。

## 重新发送提示并观察更改

1. 返回 Copilot Chat，选择 **New Chat** 清除上下文。
2. 再次打开 `src/lib/publishers.ts`，让 Copilot 聚焦正确文件。
3. 发送与之前**相同的提示**：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 注意生成的文件现在以类似以下注释块开头：

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 注意生成的函数现在包含类似以下 TSDoc 注释：

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

你已指导 Copilot 遵循新的项目标准，并将标准应用到后续练习会继续使用的真实代码中。

## 提交并推送指令和更改

指令文件与其他项目资源一样，使用源代码管理。现在提交并推送分支。

1. 按 <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux）打开终端。
2. 运行以下命令，确认指令和辅助函数更改已保存：

   ```bash
   git status
   ```

3. 暂存并提交指令更新和 Copilot 为辅助函数基础所做的所有更改：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示其他配套更改（例如测试），也要添加这些文件。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支已推送，现在创建拉取请求并关联待办事项中的文档标准议题。可以手动操作，也可以让 Copilot 使用已连接的 GitHub 工具查找议题、创建并合并 PR。

> [!NOTE]
> 这是第一次让 Copilot 代表你操作 GitHub。项目模板已配置 GitHub 工具，因此无需额外设置。Copilot 首次使用工具时，VS Code 可能提示登录 GitHub；按提示授权即可。后续练习会介绍这种连接（Model Context Protocol）。

1. 在 Codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确保智能体选择器中选中 **Agent**。
3. 让 Copilot 查找与更新指令文件有关的议题，并从当前分支创建 PR，说明指令更新以及为后续筛选工作添加的发行商辅助函数基础：

   ```plaintext
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. 如果 Copilot 请求允许执行 GitHub 操作，请审查命令，并酌情选择 **Allow**。
5. PR 创建后，要求 Copilot 将其合并并把本地分支切回 `main`，以获取最新代码：

   ```plaintext
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

6. 如果 Copilot 请求允许执行 GitHub 操作或运行 shell 命令，请审查命令并酌情选择 **Allow**。

现在已借助 GitHub Copilot 创建并合并拉取请求。

## 总结与后续步骤

你了解了 Copilot 如何读取项目指令，并在 VS Code Copilot Chat 中：

- 发送代码生成提示，观察 Copilot 在**现有**指令下的输出。
- 向 `.github/copilot-instructions.md` 添加新的存储库级标准。
- 重新发送相同提示，观察生成的代码遵循新标准。
- 将指令更新和辅助函数基础提交到 `main`，供下一练习使用。

接下来在[智能体模式][next-lesson]中运用这些指令，让 Copilot 跨代码库添加新功能。

## 资源

- [GitHub Copilot 自定义指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义指令的 5 个提示][copilot-instructions-five-tips]
- [GitHub Copilot 个人自定义指令][personal-instructions]
- [Awesome Copilot：指令文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
