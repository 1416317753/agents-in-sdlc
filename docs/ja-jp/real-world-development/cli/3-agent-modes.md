---
title: "レッスン 3 - エージェントモード: Plan と Autopilot"
description: "Plan で実装方針を決め、Autopilot で Issue に基づくフィルター機能を構築して、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

最初に小さな機能をプロジェクトに追加しました。より大きな変更には、さらにしっかりとしたプロセスが必要です。GitHub Copilot CLI は組織の既存の開発フローに沿って作業できます。このレッスンから、Issue に基づく機能の生成、コードの検証、動作確認、プロジェクトへのマージまで、一般的なエージェント主導の開発プロセスに沿って進めます。

> [!NOTE]
> 機能の作業では、同じ会話とブランチを続けて使います。通常はファイルの種類ごとに異なるブランチや PR を使いますが、ここでは主な概念に集中するため手順を簡略化します。

このレッスンでは、次のことを行います。

- GitHub Issue から Copilot の会話を始めます。
- Plan モードで要件を決めます。
- Autopilot モードで新しい機能を実装します。
- コードをレビューします。
- 転送したブラウザーで機能を手動で検証します。

機能の作業を続けながら、リポジトリの指示を更新し、既存の `quality-checks` スキルをカスタマイズし、MCP による検証を追加して QA エージェントを作成し、機能 PR を開きます。

## シナリオ

Tailspin Toys のカタログが増えてきたため、訪問者がカテゴリと出版社でゲームを絞り込めるようにします。バックログの Issue には機能が説明されていますが、カテゴリを組み合わせる方法など、実装前に合意する詳細があります。Plan モードで決定し、範囲を明確にした作業を Autopilot に任せます。

## 背景

AI コーディングエージェントを導入しても、開発の基本は変わりません。一般的な開発フローは次のようなものです。

1. 実施内容を説明する Issue から始めます。
2. 実装計画を作成します。
3. コードを実装してレビューします。
4. テストを実行して検証します。
5. 新しい機能を手動で確認します。
6. プルリクエスト (PR) を作成します。
7. コードレビューと継続的インテグレーションが成功したら、マージします。

> [!NOTE]
> 具体的な内容はチームや組織によって異なりますが、多くの場合は上記の流れに沿っています。

この標準的な進め方に従うことで、AI が生成したコードが要件を満たし、手書きのコードと同じレビューと検証を受けるようにできます。

## 会話モード

**会話モード**は、エージェントがどの程度自律的に作業するかを制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> を押すと、モードを切り替えられます。

- **Interactive**: ユーザーとエージェントが協力して進めます。エージェントは変更を提案し、ユーザーの入力を待ちます。
- **Plan**: エージェントが最初に計画を作成し、プロジェクトファイルの編集は行いません。
- **Autopilot**: エージェントがコードの作成、テストの実行、反復を自律的に行い、タスクの完了を目指します。

まず Plan モードで計画を確認し、その後 Autopilot で実装します。

## Issue から始める

フィルター機能の作業に取り掛かる前に、Codespace とリポジトリを準備します。

1. Codespace に戻ります。停止している場合は再起動します。
2. 星評価の PR がマージ済みであることを確認します。
3. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押します。
4. `main` を更新し、フィルター機能用のブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を 2 回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての Issue を表示します。
8. 矢印キーで **Allow users to filter games by category and publisher** というタイトルの Issue を選択します。
9. <kbd>C</kbd> を押して Issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトの先頭に `#7` (または同様の番号) が追加されます。`#` を使うと、GitHub 上の Issue やプルリクエスト (PR) をコンテキストに含められます。

## フィルター機能を計画する

計画を立てることで、Copilot に作業を任せる前に機能の実装方法やタスクの進め方を決められます。複雑な作業では、時間をかけて計画することをお勧めします。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> で Plan モードに切り替え、プロンプトの下に **Plan** と表示されていることを確認します。
2. 前の手順で追加した Issue の後ろに、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot はプロジェクトを調べて、適切な実装方法を判断します。

3. フィルターの動作について質問された場合は、希望に基づいて回答します。唯一の正解はありません。
4. 計画が完成したら、<kbd>Control</kbd>+<kbd>E</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) を押して計画全体を表示します。
5. 計画を読み、判断と一致しないところがあれば修正を依頼します。

## Autopilot を承認する

計画を確認したら、Autopilot を使って実装します。Autopilot は、タスクが完了したと判断するまで問題に取り組みます。

1. **Accept plan and build on autopilot (recommended)** またはインストール済みバージョンに表示される同様の選択肢を選びます。
2. モード表示に **Autopilot** と表示されることを確認します。
3. Copilot が計画に沿って反復し、コードを生成してテストを実行する様子を確認します。

> [!NOTE]
> 計画を承認すると、すぐに実装が始まる場合があります。必ず計画を確認してから承認してください。依存関係が見つからない場合やポートが競合する場合は、チェック完了と判断する前にセットアップの問題を解決します。

## 実装をレビューして検証する

生成されたコードは、ほかのコードと同様にマージ前にレビューします。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードに切り替え、モード表示が **Plan** または **Autopilot** ではないことを確認します。
2. `/diff` を入力し、フィルター機能とテストを確認します。
3. Issue と、計画中に決めた内容に照らして変更を確認します。
4. レビュー後、<kbd>Esc</kbd> を押して差分表示を閉じます。
5. プロジェクトのチェック結果を確認し、失敗があれば Copilot に修正を依頼します。

## 新しい機能を確認する

コードのレビューができたら、アプリを起動して Codespaces の転送ポートからサイトを開きます。

1. Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces でポート `4321` が利用可能になったら **Open in Browser** を選択します。
3. 計画で決めたカテゴリと出版社のフィルター、それらの組み合わせを試します。
4. リセットと結果が空になる場合の動作が、Issue と決定した内容に一致することを確認します。
5. Codespace に戻り、Copilot に起動した開発サーバーを停止するよう依頼します。

## まとめと次のステップ

会話モードを使い分けて、機能を実装しレビューしました。このレッスンでは、Issue から会話を開始し、Plan モードで要件を決め、Autopilot モードで実装し、コードをレビューして、転送したブラウザーで手動検証しました。

次は[カスタム指示][next-lesson]を使い、コードが文書化された規約に従うようにします。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンドリファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
