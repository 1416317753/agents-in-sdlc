---
title: "演習 2 - Copilot Agent Mode で新機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

単純なアプリの変更でも、通常は複数のファイルを更新し、テストを実行する必要があります。開発者は必要なファイルを探して変更し、テストを実行して問題をデバッグし、更新し忘れたファイルを見つけて再度変更します。このような作業に役立つのが Copilot Agent Mode です。

Copilot Agent Mode は、IDE 内でより自律的に作業します。既存のプロジェクト構造を調べ、必要なファイルを更新し、テストなどのタスクを実行して、見つかった問題を自動的に修正します。Agent Mode を使ってサイトに機能を追加しましょう。

> [!NOTE]
> 名前は似ていますが、agent mode と cloud agent は異なる体験を提供します。Agent mode は IDE 内で作業するため、すばやいフィードバックとやり取りができます。Cloud agent はチームメンバーのように非同期で作業し、Issue やプルリクエストを通じて連携します。

この演習では、次のことを学びます。

- Copilot Agent Mode がプロジェクトを調べ、関連ファイルを見つけ、複数ファイルを連携して変更する方法
- Copilot Agent Mode で UI とデータ層にまたがる新機能を実装する方法
- コードベースにマージする前に、Copilot Agent Mode が作成した変更とテストをレビューする方法

## シナリオ

ゲームの一覧が増えてきたため、カテゴリと出版社で絞り込めるようにします。前の演習では publishers ヘルパーを追加しました。次は Copilot Agent Mode を使い、残りのデータ層、UI、テストを仕上げます。

## Tailspin Toys の Web サイトを実行する

変更を加える前に、現在の機能を確認します。Tailspin Toys は、開発者をテーマにしたボードゲームのクラウドファンディングプラットフォームです。ゲーム一覧と詳細を表示します。サイトは単一の Astro アプリで、ビルド時に静的 HTML を生成します。ページは frontmatter 内で Drizzle ORM を使ってローカル SQLite データベースを直接参照します。独立したバックエンド API やクライアントサイド UI フレームワークはありません。再利用するデータアクセスヘルパーは `src/lib/` にあり、インタラクティブな動作には標準 DOM API を使う小さな Astro `<script>` を使用します。

### Web サイトを起動する

`npm run dev` タスクで、単一の Astro 開発サーバーを起動できます。GitHub Codespace で次の手順を実行します。

1. 前の演習の続きとして Codespace に戻ります。`main` ブランチにいることを確認します。
2. Codespace 内で <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押し、新しいターミナルを開きます。
3. フィルター作業用のブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドで Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro の開発サーバーが起動すると、次のような URL が表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. ターミナルに表示された `http://localhost:4321` を、<kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら選択して Web サイトを開きます。

> [!NOTE]
> Codespace のターミナルで localhost の URL を選択すると、自動的に `https://<your-codespace-name>-4321.app.github.dev/` にリダイレクトされます。これは Codespace で実行している Web サーバーへのプライベートトンネルです。

### Web サイトを確認する

Web サイトを起動したら、次の主な機能を確認します。

- **ホームページ**: タイトル、画像、説明とともにボードゲームの一覧を表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、出版社、カテゴリなどの情報を表示します。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めてください。モードとモデルの選択は演習ごとに異なります。必要な場合は手順で説明します。

サイトは動作しますが、新しい機能を追加して改善します。まずバックログを確認します。テンプレートからプロジェクトを設定すると Issue のバックログが自動作成されています。GitHub Copilot に内容を表示するよう依頼します。

1. Chat ビューのエージェントドロップダウンで **Agent** を選択します。Agent は複数ファイルにわたる変更を計画・実装し、ターミナルコマンドを実行してツールを呼び出します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデル一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨するものではありません。ワークショップ作成時に Claude Sonnet 4.5 を使用したため、手順に含めています。提案を比較的安定させることを目的としていますが、LLM の動作は確率的であり、説明と異なる提案が表示される場合があります。これは通常の動作です。

> [!NOTE]
> LLM の確率的な性質により、Copilot が別の MCP コマンドを使用する場合がありますが、タスクを完了できるはずです。

3. 次のプロンプトを Copilot に送信して、Issue のバックログを確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択し、すべての Issue を一覧表示するコマンドを実行します。
5. 生成された Issue の一覧を確認します。

Copilot がユーザーに最も役立つと考える項目を優先順位付けしたことを確認してください。

## 指示ファイルを確認する

コードの生成を依頼する前に、Copilot にコンテキストを与える指示ファイルを確認します。この演習では、Web サイトへの機能追加方法に関する情報を含む [UI 指示ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)を使います。

1. Codespace で `.github/instructions/ui.instructions.md` に移動します。
2. 機能追加に関する全体的なガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要
   - コンポーネントの設計、テスト容易性、アクセシビリティの原則
   - Astro、Tailwind CSS、Drizzle などのファイルに関する指示へのリンク

> [!TIP]
> 指示ファイルから、ほかの指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルの場所からの相対パスです。指示を再利用し、複雑な内容を管理しやすい単位に分け、例やテンプレートを示せます。

## フィルター機能を実装する

フィルター機能を完成させるには、少なくとも次の 3 つを更新します。

- データ層 (`src/lib/`) にフィルター処理を追加または調整する
- フィルター動作のテストを追加または更新する
- ゲーム一覧ページにフィルター UI を追加する

コードベースにマージする前にテストを実行し、成功する必要があります。Copilot Agent Mode にこれらの作業を依頼します。

1. Copilot との現在の会話を続けるか、**New Chat** を選んで新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択し、指示ファイルを追加します。

   ![UI 指示ファイルの選択例](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェントドロップダウンで **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. モデルが **Claude Sonnet 4.5** のままであることを確認します。
5. バックログの関連 Issue に基づいて機能を実装するよう、次のプロンプトを送信します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調べ、データ層のヘルパー、UI、テストなど、関連ファイルを見つけて更新する様子を確認します。

   ![Copilot がプロジェクトのファイルを調べる様子](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの変更、テストの実行などを行うため、タスクの複雑さやコードベースによって数分かかることがあります。作業中、エディターに **Keep** と **Undo** が表示される場合があります。Copilot が完了すると、すべての変更に対して操作を選べるようになるため、作業中に選択する必要はありません。

7. Copilot に促されたら **Continue** を選択し、テストを実行します。

   ![Copilot Chat のテスト実行確認ダイアログ](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストに失敗したりすることがあります。Copilot はコード生成とテストを繰り返し、エラーが見つからなくなるまで修正します。

   ![Copilot Agent Mode のチャットセッションが完了した状態](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを開いているブラウザーに戻り、新しい機能を試します。
11. 機能が動作し、コードのレビューも済んだら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

この演習では、GitHub Copilot Agent Mode を使って Tailspin Toys に機能を追加しました。次のことを学びました。

- GitHub Copilot Agent Mode で UI とデータ層にまたがる新機能を実装する方法
- Copilot Agent Mode がプロジェクトを調べ、関連ファイルを見つけ、複数ファイルを連携して変更する方法
- コードベースにマージする前に、生成された変更とテストをレビューする方法

次は [Playwright MCP server][next-lesson]で機能をテストし、プルリクエストを作成します。

### 追加の演習 — ページ分割を実装する

ゲーム一覧が増えるとページ分割が必要になります。この演習で学んだことを使い、Copilot にページ分割の実装を依頼してみましょう。

- 既存の指示ファイルを使うなど、現在のベストプラクティスに従います。
- ユーザーがページサイズを選べるようにするか、固定にするかを検討します。
- 希望する実装に必要なガイダンスをプロンプトに含めます。
- 必要に応じて Copilot に変更を依頼し、コンテキストを追加します。これは Copilot を使うときの通常の進め方です。

## リソース

- [Copilot の ask、edit、agent mode: それぞれの役割と使い分け][choose-mode]
- [VS Code の agent mode][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
