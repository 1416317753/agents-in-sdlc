---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

直前のいくつかの演習では、ユーザー体験の改善と機能追加を目的とする 3 つのタスクを GitHub Copilot cloud agent に依頼しました。cloud agent は非同期かつ自律的に動作しますが、タスクを監視することも重要です。

cloud agent に割り当てたタスクは、GitHub.com の[エージェントページ][agents-page]など、さまざまなツールで管理できます。このミッションコントロールでは、オープンなプルリクエスト (PR) があるすべてのエージェントタスクを確認できます。実行された操作を調べたり、進行中のセッションに追加の指示を与えたりすることもできます。

このレッスンでは、次のことを行います。

- エージェントページで cloud agent のタスクを監視します。
- 進行中のセッションに指示を追加し、機能を依頼します。

## シナリオ

チームはハイコントラストモードの作成を依頼した後、ライトモードも追加するよい機会だと気付きました。サイトのスタイルと切り替え機能を更新しているため、ライトモードも含めるのが自然です。エージェントの作業を案内し、ハイコントラストモードとライトモードの両方を追加してもらいます。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの現在の状況を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)に移動します。
2. メインペインと左ペインの両方でタスク一覧を確認します。次のような、Copilot に割り当てたタスクが表示されます。
    - コードベースのドキュメントの更新
    - ゲームを変更するための API の生成
    - Web サイトへのハイコントラストモードの追加
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。たとえば、次の操作が表示されます。
    - リポジトリからのコードのチェックアウト
    - Copilot が作業する環境の作成
    - MCP サーバーのセットアップ
    - 割り当てられたタスクを完了するための各種手順

> [!NOTE]
> 表示される手順は、Copilot の作業状況や採用した方法によって異なります。

4. 右側に表示されるプルリクエスト (PR) ペインも確認してください。ここでは、PR と変更されたファイルを確認し、進行状況を監視できます。

## cloud agent に指示を追加する

実行中のタスクを確認したので、エージェントがハイコントラストモードを実装している間に、ライトモードの切り替えも追加するよう依頼します。

1. ハイコントラストモードの追加に関するセッションを選択します。正確なタイトルは Copilot が付けた名前や作業状況によって異なります。

    ![ミッションコントロールのアクセシビリティセッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了し、作業が始まったことを示すメッセージが表示されるまで、数分間セッションを確認します。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![Copilot のエージェントページで、Steer active session while Copilot is working ダイアログが強調表示されています](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業計画に含めたことを確認します。

## Copilot の作業を待つ

これまでと同様に、Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で進行中だった手順が終わると、新しい依頼が作業計画に反映されます。

完了までには数分かかります。その間に、これまでに学んだことを振り返りましょう。

## まとめと次のステップ

このレッスンでは、GitHub Copilot cloud agent のタスクを監視し、案内するための中心的な場所である Copilot agents ページについて学びました。このミッションコントロールを使うと、進行中および完了したタスクの追跡、実行中の作業のレビュー、進行中のタスクの範囲の変更や追加指示を行えます。

次の内容を学びました。

- ミッションコントロールとエージェントページで cloud agent のタスクを監視する方法
- 進行中のセッションに追加の機能を依頼する方法

Copilot がアクセシビリティ機能を完成させる間に、Copilot が作成したプルリクエストを[反復して改善する][next-lesson]方法を学びましょう。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復して改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
