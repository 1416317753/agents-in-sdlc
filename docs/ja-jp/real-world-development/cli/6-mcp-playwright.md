---
title: "レッスン 6 - Playwright MCP で機能を検証する"
description: "Copilot CLI で Playwright MCP を確認または設定し、ブラウザーを使ってフィルター機能をテストします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

これまでに説明したように、コードを書くこと以外にも必要な作業があります。データや外部サービスを扱い、Copilot が追加の自動化を利用できるようにすることもあります。MCP server を使うと、Copilot CLI に組み込まれている機能を超えて、さらに多くのツールやサービスを利用できます。

このレッスンでは、次のことを行います。

- Model Context Protocol (MCP) と Copilot CLI での使い方を学びます。
- Playwright MCP server がまだ利用できない場合は追加します。
- エージェントにブラウザーを操作させ、フィルター機能を確認します。

## シナリオ

ユニットテストとエンドツーエンドテストは重要ですが、UI の変更を検証するには、実際に UI を操作する必要があります。Copilot がユーザーと同じように Web サイトを使えるようにし、期待どおりの動作を確かめます。

## Model Context Protocol (MCP) とは

[Model Context Protocol (MCP)][mcp-blog-post] は、AI エージェントが外部のツールやサービスと通信する方法を提供します。MCP を使うと、AI エージェントは最新の情報にアクセスし、ユーザーに代わって操作できます。

ツールやリソースには、AI エージェントと外部ツールやサービスの間を仲介する MCP server を通じてアクセスします。各 MCP server は、エージェントが利用できるツールとリソースを提供します。

- **[GitHub MCP Server][github-mcp]**: GitHub リポジトリ、Issue、プルリクエストを管理する API にアクセスできます。
- **[Playwright MCP Server][playwright-mcp-server]**: Playwright によるブラウザー自動化を提供します。

ほかにも多くの MCP server が利用できます。GitHub は、MCP を見つけてエコシステムに貢献できる [MCP registry][mcp-registry] を提供しています。

> [!CAUTION]
> MCP server はほかの依存関係と同じように扱ってください。使用前にソースコードを確認し、発行元を検証して、セキュリティへの影響を検討してください。

## Playwright MCP server を追加する

Playwright MCP server がまだ利用できない場合は、Copilot がユーザーと同じように Web サイトを操作できるように追加します。

> [!NOTE]
> Copilot CLI ですでに Playwright MCP を設定している場合は、このセットアップを省略して次のセクションに進みます。

1. Codespace に戻ります。
2. Copilot CLI で次のコマンドを入力し、MCP server の追加ダイアログを開きます。

   ```plaintext
   /mcp add
   ```

3. 名前に `playwright` と入力し、<kbd>Tab</kbd> を押します。
4. <kbd>Enter</kbd> を押して server の種類に **STDIO** を選び、<kbd>Tab</kbd> を押します。
5. **Command** ダイアログに次の内容を貼り付けます。

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. <kbd>Control</kbd>+<kbd>S</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>S</kbd> (Windows/Linux) を押して、新しい MCP server を保存します。
7. <kbd>Esc</kbd> を押して MCP ダイアログを閉じます。

## Playwright で Copilot に機能を確認させる

これまでは機能の動作を手動で確認しました。今度は Copilot で利用できる Playwright MCP server を使います。

1. 次のプロンプトで、Playwright MCP server を使って機能を検証するよう Copilot に指示します。

   ```plaintext
   Start the app and use Playwright MCP to check filtering against the issue and our plan. Tell me what works and what doesn't, without making changes. Stop the server you started when you're done.
   ```

> [!NOTE]
> Copilot は通常、自動的に MCP server を見つけて使うため、明示的に指定しなくてもかまいません。使うツールを伝えると、結果が安定しやすくなり、トークンも節約できます。

2. Copilot がブラウザー上で機能を確認する手順を表示する様子を確認します。
3. レポートを読み、すべての動作が期待どおりか確認します。

Copilot はアプリを起動し、Playwright で Web サイトを操作し、作業後にサーバーを停止して結果を報告します。

## まとめと次のステップ

Copilot CLI から Playwright MCP server を使い、実際のブラウザーで機能を確認しました。このレッスンでは、MCP と Copilot CLI での使い方を学び、必要に応じて Playwright MCP server を追加し、エージェントにブラウザーを操作させました。

次はスキルとブラウザーツールを専門的な役割にまとめるため、[QA カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ話題になっているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [Copilot CLI に MCP server を追加する][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
