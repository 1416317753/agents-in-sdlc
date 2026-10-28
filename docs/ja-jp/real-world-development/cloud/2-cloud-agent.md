---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

多くの組織が技術的負債に悩んでいます。未解決のセキュリティ課題、更新が必要なレガシーコード、実装する時間がなくバックログに残った機能要望などがあります。GitHub Copilot の cloud agent は、コードの更新や機能の追加などのタスクを自律的に実行できます。作業が終わると、人間の開発者がレビューできるドラフト PR を作成します。これにより、時間のかかる作業を任せ、開発者はより大きな課題に集中できます。

この演習では、Copilot cloud agent を使って次のことを学びます。

- コード生成環境のカスタマイズ
- 操作を安全に行うことの重要性
- 範囲を明確に定めた Issue の重要性
- Issue を Copilot に割り当てる方法

## シナリオ

Tailspin Toys では、最初のサイトを開発した委託先がドキュメントを十分に作成していなかったため、技術的負債になっています。まず、アプリケーション内のエクスポートされたすべての関数に TSDoc doc comment を追加します。

また、デザインチームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリに属する関連ゲームを表示します。完成度の高い実装はまだ必要ありません。ユーザー体験の受け入れテストに使える状態を目指します。こちらは現在ブロッカーですが、優先度がより高い別の Issue もあります。

どちらも、優先順位が下がりやすいタスクです。Copilot cloud agent に非同期で作業を依頼し、別の作業を進めてから、戻って成果をレビューできます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同じようにバックグラウンドでタスクを実行できます。[GitHub Issue を Copilot に割り当てる][assign-issue]と、Copilot は進行状況を追跡するドラフトプルリクエストを作成し、環境を準備して、タスクに取り組み始めます。作業中でも完了後でもセッションを確認できます。提案された解決策のレビュー準備ができると、プルリクエストで通知されます。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。単純に見える操作でも、詳しく見ると複雑なことがあります。[Copilot cloud agent に依頼するタスクの範囲][cloud-agent-best-practices]を慎重に決めてください。AI ペアプログラマーとして扱い、段階的に作業し、学び、試し、必要に応じて調整します。生成 AI を利用しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示とその働きを学びました。カスタム指示を実際に使った場合は `.github/copilot-instructions.md` の効果を確認しています。まだの場合は、cloud agent に作業を依頼する前に、このリポジトリに含まれる指示ファイルを確認してください。

リポジトリの GitHub Web UI、または Codespace で次のファイルを読み取り専用で確認します。

- `.github/copilot-instructions.md`: **Code standards** セクション、特に TypeScript の規約と TSDoc doc comment の要件を確認します。
- `.github/instructions/unit-tests.instructions.md`: `applyTo` frontmatter が `**/*.test.ts` ファイルに指示を適用することを確認します。

次のセクションで `Code lacks documentation` Issue を cloud agent に割り当てるときは、作成されるプルリクエストに TSDoc doc comment、TypeScript の規約、コメントヘッダーが反映されることを確認します。後の演習で PR をレビューするときにも取り上げます。

## Copilot cloud agent 用の開発環境を設定する

コードの作成では、通常、適切な環境とセットアップ用スクリプトが必要です。タスクを Copilot に割り当てる場合も同じです。Copilot はソフトウェアエンジニアのように作業するためです。

Cloud agent は作業環境に [GitHub Actions][github-actions] を使います。`.github/workflows/copilot-setup-steps.yml` に設定する[専用のセットアップワークフロー][setup-workflow]を作成すると、作業開始前に必要な開発ツールや依存関係を用意できます。このラボの流れを簡単にするため、あらかじめ設定されています。単一の Astro アプリ用に Node.js、プロジェクトの依存関係、エンドツーエンドテスト用のブラウザーバイナリ、マイグレーションとシードが済んだ SQLite データベースを用意します。

```yaml
name: "Copilot Setup Steps"

# リポジトリの "Actions" タブからセットアップ手順をテストできます
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # *各ステップ*に必要な最小限のアクセス許可を設定します。Copilot の操作には専用のトークンが付与されます。
    permissions:
      # 依存関係のインストールなど、セットアップ手順の一部としてリポジトリをクローンする場合は `contents: read` 権限が必要です。ここでクローンしない場合は、手順の完了後に Copilot が自動的にクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体は Astro + Drizzle/libSQL です)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # ビルドとテスト用のデータを用意するため、ローカル SQLite データベースをマイグレーションしてシードします。
      - name: Set up the database
        run: npm run db:setup
```

このワークフローは、通常の GitHub ワークフローと同じように見えます。次の点を確認してください。

- `copilot-setup-steps` という単一のジョブがあります。Copilot がプルリクエストの作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーがあるため、リポジトリの **Actions** タブから手動で実行できます。Copilot の実行を待たずに、ワークフローが正常に動くかテストできます。

## ドキュメントを追加する

多くのプロジェクトでは、ドキュメントが古いか、まったくないことが技術的負債となり、生産性を下げ、コードベースの保守や新しい開発者の受け入れを難しくしています。Copilot はドキュメントの作成を得意とするため、この作業は cloud agent に割り当てるのに適しています。Copilot はバックグラウンドで必要なドキュメントを作成します。後の演習で結果をレビューします。

1. 新しいブラウザータブで github.com のリポジトリに移動します。
2. **Issues** タブ、続いて **New issue** を選択します。
3. **Blank issue** を選択します。
4. **Title** に `Code lacks documentation` と入力します。
5. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

6. **Create** を選択して Issue を作成します。
7. 右側の **Assign to Copilot** を選択して、割り当てダイアログを開きます。

    ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

8. **Assign** を選択します。

    ![Copilot への割り当ての詳細](../../../_images/ex4-assign-copilot-details.png)

9. **Pull Requests** タブを選び、新しいプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものになります。一覧に PR が表示されない場合は、少し待ってからブラウザーを更新してください。
10. 数分後、Copilot が TODO リストを作成したことを確認します。

> [!NOTE]
> Copilot が PR に TODO リストを表示するまで数分かかることがあります。Copilot は環境を作成し (前述のワークフローを実行します)、プロジェクトを分析して、タスクに取り組む方法を決めています。

11. リストと、Copilot が実行する予定のタスクを確認します。
12. プルリクエストのタイムラインを下にスクロールし、Copilot が Issue の作業を開始したことを確認します。
13. **View session** ボタンを選択します。

    ![Copilot のセッションビュー](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新された表示を確認するため、ウィンドウを更新する必要がある場合があります。

14. ライブセッションをスクロールし、Copilot が問題を解決する様子を確認します。コードを調べて状態を理解し、適切な計画を考え、コードを作成します。

完了までには数分かかることがあります。cloud agent の主な利点は、タスクを非同期に実行している間に別の作業を進められることです。もう 1 つのタスクを cloud agent に割り当ててから、アプリに機能を追加する作業を始めましょう。

## ゲーム詳細ページに関連ゲームを追加する

デザインチームの要望に対応する間に、別の作業を進められることも Copilot cloud agent の利点です。関連ゲームの表示機能に時間がかからない場合でも、その間に別の作業ができます。Copilot cloud agent に依頼しましょう。

1. github.com のリポジトリに戻り、**Issues** タブを選択します。
2. **New issue**、続いて **Blank issue** を選択します。
3. **Title** に `Show related games on the game details page` と入力します。
4. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

5. **Create** を選択して Issue を作成します。
6. 右側の **Assign to Copilot** を選択して、割り当てダイアログを開きます。

    ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

7. **Assign** を選択します。

しばらくすると、Issue の最初のコメントに 👀 が表示され、Copilot が作業を始めたことが分かります。

![Copilot が Issue に取り組んでいることを目の絵文字で示しています](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼にも取り組んでいます。Copilot cloud agent はソフトウェアエンジニアと同じように作業するため、常に監視する必要はありません。作業が終わったらレビューします。次はカスタムエージェントの作成と使用を学びましょう。

## まとめと次のステップ

このレッスンでは [GitHub Copilot cloud agent][copilot-agents]について学びました。cloud agent に Issue を割り当てると、非同期に作業を進めてもらえます。技術的負債の解消、新しい機能の作成、別のフレームワークへのコード移行などに利用できます。

次の内容を学びました。

- コード生成環境のカスタマイズ
- 操作を安全に行うことの重要性
- 範囲を明確に定めた Issue の重要性
- Issue を Copilot に割り当てる方法

cloud agent がバックグラウンドで作業している間に、次はカスタムエージェントを作成して使用します。[Copilot cloud agent は MCP server も使用でき][cloud-agent-mcp]、前のモジュールで学んだカスタム指示にも対応しています。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローに関するベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
