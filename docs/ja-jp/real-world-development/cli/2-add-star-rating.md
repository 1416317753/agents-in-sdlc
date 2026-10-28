---
title: "レッスン 2 - 星評価を追加して手早く成果を出す"
description: "Copilot CLI でゲームカードに小さな変更を加え、転送したブラウザーで確認して、最初のプルリクエストをマージします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

Copilot CLI をインストールして会話を試したので、最初の変更をプロジェクトに加えましょう。変更は小さくします。ゲームデータにはすでに星評価がありますが、ホームページのゲームカードには表示されていません。エージェントに表示を依頼し、変更をレビューして、最初のプルリクエストとしてマージします。

このレッスンでは、次のことを行います。

- 機能ブランチで対象を絞った Copilot の会話を開始します。
- エージェントに小さな変更を依頼します。
- `/diff` で変更をレビューします。
- アプリを起動し、転送されたブラウザーで確認します。
- 最初のプルリクエストを作成してマージします。

## シナリオ

Tailspin Toys の各ゲームには星評価があり、ゲーム詳細ページにはすでに表示されています。しかし、ホームページのゲームカードにはタイトル、カテゴリ、出版社、説明だけが表示されます。最初の練習として、既存の評価を各カードに表示します。最初のセッションに適した、小さく独立した変更です。

## 会話の構成

**会話**では、Copilot CLI を使ってタスクに取り組みます。Copilot app と異なり、通常の CLI 会話では専用の worktree ではなく、ターミナルでチェックアウト中のリポジトリと Git ブランチを使います。保存された会話を再開しても、ファイルとブランチはディスク上の通常の Git 状態として残ります。

会話では、プロンプトとエージェントの応答、ファイルを調べて編集するツールの動作、`/diff` で確認できる変更の 3 つを確認できます。

## 会話を開始して変更を依頼する

新しい会話を始めて機能を実装します。

1. Codespace に戻ります。
2. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押します。
3. Copilot を起動します。

   ```bash
   copilot --yolo
   ```

4. `/new` スラッシュコマンドを使い、<kbd>Enter</kbd> を押して新しいセッションを開始します。
5. 次のプロンプトで変更を依頼します。

   ```plaintext
   Show each game's starRating out of 5 in the game cards on the list page. If the rating is null, show "No rating yet". Keep the card layout as it is, add tests, and run the relevant checks.
   ```

Copilot はプロジェクトを調べ、ゲーム詳細の表示に使うファイルを見つけて必要なコードを作成します。Copilot CLI で新機能を追加できました。

## 差分をレビューする

小さな変更でも、マージする前に AI が生成した変更をレビューします。

1. `/diff` を入力して、変更されたすべてのファイルを確認します。
2. 評価がある場合は数値が表示され、`starRating` が `null` の場合は `No rating yet` と表示されることを確認します。
3. テストが両方の状態を対象にしていることを確認します。
4. Copilot が実行したチェックの結果を確認し、失敗があれば修正を依頼します。
5. レビューが終わったら <kbd>Esc</kbd> を押して差分画面を閉じます。

> [!NOTE]
> 生成 AI ツールと同様に、Copilot の動作は確率的です。コードが正確に同じになるとは限りません。特定の実装に固執せず、動作をレビューしてください。

## 変更を確認する

コードを読むだけでなく、実際に動くことを確認します。Copilot に Web サイトを起動してもらい、Codespaces で転送されたブラウザーから UI を確認します。

1. Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can inspect the star-rating change in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces でポート `4321` が利用可能になったら **Open in Browser** を選択します。
3. ゲームカードに 5 段階評価が表示されることを確認します。
4. Copilot に戻り、起動したサーバーを停止するよう依頼します。

   ```plaintext
   Stop the development server you started.
   ```

## 最初のプルリクエストを作成してマージする

機能ができたので、プロジェクトにコードをマージするプルリクエスト (PR) を作成します。

1. 既定のエージェントに、変更をコミットするよう依頼します。

   ```plaintext
   Commit the reviewed star-rating changes with an appropriate commit message.
   ```

2. `/pr create` を入力します。Copilot CLI は PR の作成時に既存のコミットをプッシュし、PR の URL を表示します。
3. <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら Copilot CLI に表示された URL を選択し、PR を開きます。
4. 変更されたファイルとチェックをレビューします。
5. 準備ができたら **Merge pull request** を選択し、マージを確定します。
6. Codespace に戻り、`/exit` で Copilot CLI を終了します。
7. ローカルの `main` を更新します。

   ```bash
   git checkout main
   git pull
   ```

## まとめと次のステップ

GitHub Copilot CLI を使って最初の変更をリリースしました。このレッスンでは、次のことを行いました。

- 機能ブランチで Copilot の会話を始めました。
- ゲームカードに小さな変更を依頼しました。
- `/diff` で変更をレビューしました。
- アプリを起動し、転送したブラウザーで星評価を確認しました。
- 最初のプルリクエストを作成してマージしました。

次はフィルター Issue から始め、Plan と Autopilot モードで[大きな機能を作成します][next-lesson]。

## リソース

- [GitHub Copilot CLI について][about-copilot-cli]
- [Copilot CLI コマンドリファレンス][cli-reference]

[previous-lesson]: ../1-install-copilot-cli/
[next-lesson]: ../3-agent-modes/
[about-copilot-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
