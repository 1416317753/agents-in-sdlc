---
title: "第 4 课 - 使用自定义指令指导 Copilot"
description: "了解存储库指令，添加文档标准，并将其应用到筛选代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，应确保 Copilot 能获取相关指导。[指令文件][instruction-files]不仅说明要编写什么代码，还会说明代码应如何组织。现在已构建筛选功能，接下来了解 Copilot 使用的指令、添加文档标准，并将其应用到代码中。

本课将介绍如何：

- 了解存储库指令和按路径限定的指令文件如何传递给智能体。
- 更新指令文件，确保遵循编码标准。
- 观察指令文件对代码的影响。

## 场景

Tailspin Toys 有一套开发实践准则，包括：

- 注释应解释意图和不明显的决策，而不是重复代码。
- `db/` 和 `src/lib/` 中导出的函数应使用 TSDoc/JSDoc 说明用途、参数和返回值；如果有可注入的 `db` 参数，也要记录。
- 可复用 Astro 组件应说明其 `Props` 接口；相关代码更改时，注释也应保持更新。
- 保留现有格式和 lint 指南。

使用指令文件可确保 Copilot 按照这些实践完成任务。

## 指令文件

自定义指令可向 Copilot 提供上下文和偏好，帮助它了解你的编码风格和要求。可指定编码约定、库以及注释风格等偏好。可以创建适用于整个存储库的指令，也可以创建适用于特定文件类型或任务的指令。

有两种指令文件：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此单一指令文件。内容应是适用于大多数请求的项目级信息。
- `.github/instructions/*.instructions.md`：为特定语言、文件类型或任务提供指导。

> [!NOTE]
> 其他指令格式及其支持情况因环境而异。依赖特定格式前，请查看[自定义指令支持参考][custom-instructions-support]。

## 查看项目中的自定义指令文件

项目已提供一组指令文件。先查看现有内容，再进行更改，以便了解更改的效果。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而非终端）中打开 `.github/copilot-instructions.md`。
3. 查看文件中的项目简介和编码指导。此文件适用于该存储库中的每次 Copilot 交互。
4. 打开 `.github/instructions` 文件夹，查看其中的文件；其中包含 Astro、Drizzle 数据层、测试等方面的指令。
5. 打开 `.github/instructions/unit-tests.instructions.md`。留意顶部的 `applyTo` 字段，它设置了决定适用文件范围的 glob。
6. 打开 `.github/instructions/drizzle.instructions.md`，查看它对其他指令文件和项目现有文件的链接。较大的指令集可以拆分为可复用的小文件，并链接到供 Copilot 遵循的示例。

## 更新指令以符合团队准则

现有文件是不错的起点，但仍缺少一项要求。修改核心 `copilot-instructions.md`，要求为新生成的 TypeScript 添加 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分（大约第 35 行）。
2. 在该部分末尾添加以下项目：

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

文件会自动保存。

## 使用更新后的指导

Copilot CLI 在对话开始时加载存储库指令。保存后，退出并恢复筛选对话，让 Copilot 在保留功能上下文的同时加载新指导。

1. 如果筛选对话仍处于打开状态，输入 `/exit` 关闭。Copilot CLI 会自动保存对话。
2. 在 `game-filters-cli` 分支的存储库根目录恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果出现提示，请选择筛选对话，并确认已检出 `game-filters-cli` 分支。
3. 让 Copilot 应用更新后的指导：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff` 并检查更改的 TypeScript 文件。确认新生成的 TSDoc 注释准确解释了代码。

## 总结与后续步骤

你已了解 Copilot 如何读取项目指令，并将新标准应用到功能中。本课中，你：

- 了解了存储库指令和按路径限定的指令文件如何传递给智能体。
- 更新了指令文件，确保遵循编码标准。
- 观察了指令文件对代码的影响。

接下来[自定义并运行可复用的 quality-checks 技能][next-lesson]，确保一致地运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义指令][instruction-files]
- [自定义指令支持][custom-instructions-support]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [Awesome Copilot：指令文件及其他资源集合][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
