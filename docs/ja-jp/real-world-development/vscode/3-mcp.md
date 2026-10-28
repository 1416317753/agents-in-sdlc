---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: Agent mode][previous-lesson] · [次のレッスン: カスタムエージェント →][next-lesson]

agent mode でフィルター機能を実装しました。プルリクエストを作成する前に、ブラウザーで実際に動作することを確認しましょう。自分でアプリを操作する代わりに、**Playwright MCP server** を接続し、Copilot に実際のブラウザーでテストしてもらいます。その後、ブランチを公開して PR を作成します。

この演習では、次のことを学びます。

- Model Context Protocol (MCP) と、MCP server が Copilot にツールを追加する仕組み
- Playwright MCP server をワークスペースに追加する方法
- Copilot にブラウザーでフィルター機能を手動テストさせる方法
- ブランチを公開し、フィルター機能のプルリクエストを作成する方法

## Model Context Protocol (MCP) とは

エージェントがエディターの外部にあるツールへアクセスできると、agent mode はさらに便利になります。Model Context Protocol (MCP) は Copilot が外部ツールやサービスと通信するための標準的な方法です。

![agent mode の内部構造と、MCP server や VS Code 拡張機能が提供するツールを含むコンテキスト、LLM、ツールとのやり取りを示す図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) は、AI エージェントが外部のツールやサービスと通信する方法を提供します。MCP を使うと、AI エージェントはリソースを通じて最新情報にアクセスしたり、ツールを使ってユーザーに代わって操作したりできます。

これらのツールやリソースには、AI エージェントと外部ツールやサービスの間を仲介する MCP server を通じてアクセスします。MCP server は、既存の API や NPM パッケージなどのローカルツールとの通信を管理します。各 MCP server は、AI エージェントが利用できるツールとリソースのセットを提供します。

よく使われている MCP server の例を紹介します。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理する API にアクセスできます。新しいリポジトリの作成、既存リポジトリの更新、Issue やプルリクエストの管理などを行えます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を提供します。Web ページへの移動、フォームへの入力、ボタンの選択などを行えます。

ほかにもさまざまなツールやリソースにアクセスできる MCP server があります。GitHub は、エコシステム内の MCP を見つけて貢献できる [MCP registry](https://github.com/mcp) を提供しています。

> [!CAUTION]
> セキュリティの観点から、MCP server はほかの依存関係と同じように扱ってください。使用前にソースコードをよく確認し、発行元を検証し、セキュリティへの影響を検討してください。信頼できる MCP server のみを使用し、機密情報や操作へのアクセスを許可する際は注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースから利用できる MCP server を設定します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. `github` server がすでに設定されていることを確認します。

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

`github` の設定はプロジェクトのテンプレートに含まれているため、前の演習で Copilot がバックログの Issue を読み取れました。[リモート GitHub MCP server][remote-github-mcp-server]を使っているため、ローカルにインストールする必要はありません。VS Code は HTTP 経由で接続し、Copilot が初めてツールを使うときに GitHub で認証します。

## Playwright MCP server を追加する

次に、2 つ目の server を追加します。[Playwright MCP server][playwright-mcp-server] は Copilot が操作できるブラウザーを提供するため、機能のテストに役立ちます。

1. `.vscode/mcp.json` で `github` と並べて `playwright` の設定を追加し、次のようにします。

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

2. ファイルを保存します。

リモートの `github` server とは異なり、`playwright` はローカル server です。VS Code が `npx @playwright/mcp@latest` を実行して起動します。`--headless` フラグを指定すると、ブラウザーを画面に表示せずに実行できます。Codespace には表示用デスクトップがないため、この設定が必要です。

> [!NOTE]
> Tailspin Toys プロジェクトではエンドツーエンドテストに Playwright を使用しているため、通常は必要なブラウザーもインストール済みです。後で Copilot がブラウザーが見つからないと報告した場合は、`npx playwright install chromium` を実行して再試行してください。

## server を起動して信頼する

VS Code は MCP server を**必要に応じて起動**します。Copilot が初めて server のツールを必要とするときに VS Code が起動し、信頼するか確認します。

1. `.vscode/mcp.json` の `playwright` 設定の上に **Start** 操作が表示されます。選択して server を起動し、構成が正しいことを確認します。
2. VS Code から server を信頼するよう求められた場合は、構成を確認して信頼する選択肢を選びます。

手動での起動は省略できます。その場合は、次の手順で Copilot がツールを初めて必要としたときに VS Code が server を起動し、信頼の確認を表示します。

## フィルター機能をテストする

[Playwright MCP server][playwright-mcp-server] を使うと、Copilot が実際のブラウザーを操作できます。ユーザーがアプリを操作して確認する代わりに、エージェントがページを開き、移動し、フィルターを適用して、結果を読み取って要約します。会話を離れずに機能を確認できます。

Playwright MCP server はスクリーンショットではなく、ページの[アクセシビリティツリー][playwright-mcp-server]を使います。エージェントは、支援技術と同じように、ボタン、リンク、リスト項目などの構造化されたラベル付き要素を判断材料にします。そのため、機能の確認を簡単なアクセシビリティチェックとしても活用できます。

server を接続し、アプリを起動した状態で、Copilot に次の手順でフィルター機能をテストするよう依頼します。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP server を使ってブラウザーを起動し、各手順を実行して結果を報告します。Issue の受け入れ条件に照らして報告を確認してください。想定どおりでない場合は、追加の質問をするか、PR を作成する前にコードの修正を依頼します。

> [!NOTE]
> テストするには、アプリを `http://localhost:4321` で実行しておく必要があります。開発サーバーを停止した場合は、プロンプトを送る前に再起動してください。Copilot が Playwright MCP server を初めて使うときにブラウザーのダウンロードが必要な場合があります。ブラウザーが見つからないという報告があれば、`npx playwright install chromium` を実行して再試行してください。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能が動作することを確認できたら、チームでレビューするためにプルリクエスト (PR) を作成します。まず `filtering-vscode` ブランチを公開します。

1. Codespace の **Source Control** パネルに移動し、Copilot が行った変更を確認します。
2. **+** アイコンを選択して変更をステージします。
3. **Sparkle** ボタンを選択してコミットメッセージを生成します。

   ![変更内容を表示する Source Control パネル](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択して、ブランチをリポジトリにプッシュします。

## プルリクエストを作成する

プルリクエストを作成する方法は、github.com や GitHub コマンドラインインターフェイス (CLI) など複数あります。今回は GitHub Copilot に関連する Issue を見つけてもらい、PR を作成して関連付けます。

1. Copilot Chat パネルに移動し、**New Chat** を選択して新しいセッションを開始します。
2. エージェントのドロップダウンで **Agent** が選択されていることを確認します。これにより Copilot は GitHub ツールを使用できます。
3. 次のプロンプトで PR の作成を依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 必要に応じて **Continue** を選択し、Copilot が情報を収集して操作を行うことを許可します。GitHub ツールを初めて使うときは、GitHub での認証を求められることがあります。案内に従ってください。
5. Copilot が Issue を検索し、該当するものを見つけて PR を作成することを確認します。
6. Copilot が生成したリンクを選択してプルリクエストをレビューします。**まだマージしないでください**。

## まとめと次のステップ

この演習では、次のことを行いました。

- Model Context Protocol (MCP) と、MCP server が Copilot にツールを追加する仕組みを学びました。
- Playwright MCP server をワークスペースに追加しました。
- リリース前に、ブラウザーでフィルター機能を手動テストしました。
- ブランチを公開し、フィルター機能のプルリクエストを作成しました。

MCP server を使って機能をテストしましたが、MCP は Copilot を拡張する方法の 1 つです。次は、アクセシビリティレビューなど、対象を絞った作業を効率化する[カスタムエージェントを作成][next-lesson]します。

## リソース

- [MCP とは何か、なぜ話題になっているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP server][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
