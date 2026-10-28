---
title: "レッスン 8 - 機能 PR の作成とマージ"
description: "フィルター機能、指示、スキルの更新、QA プロファイル、テストをまとめてレビューし、PR を作成して Agent Merge を使用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

フィルター機能、指示の更新、スキルの更新、品質保証 (QA) プロファイル、テストはすべて 1 つのブランチに保存されています。まとめてレビューし、プルリクエストを作成しましょう。星評価のプルリクエスト (PR) は手動でマージしました。今回は **Agent Merge** にマージの作業を任せます。

> [!NOTE]
> 通常は機能、指示の更新、スキルの更新、QA エージェントをいくつかの PR に分けます。このワークショップでは手順を簡潔にするため、フィルター機能と品質確認の一連の作業を 1 つの会話とブランチにまとめています。

このレッスンでは、次のことを行います。

- Agent Merge と、マージまでの作業を自動化する方法を学びます。
- 機能の変更全体と検証の証拠を確認します。
- フィルター機能の PR を作成します。
- レビュー後に Agent Merge を有効にし、PR がマージされたことを確認します。

## シナリオ

フィルター機能のワークフローを通じて、Copilot で機能を計画、実装、検証しました。次は、開発者がマージの許可を管理しながら、PR の残りの作業を自動化します。

## Agent Merge について

**Agent Merge** は、プルリクエストをマージするために必要な残りの作業を自動化します。有効にすると、Copilot は PR をブロックしている項目 (失敗した継続的インテグレーション (CI) チェックの修正、レビューコメントへの対応、必要に応じたリベースなど) に取り組みます。リポジトリで許可されている場合は GitHub の自動マージを有効にします。

これまでは **Merge pull request** を自分で選択していました。Agent Merge にその作業を任せることもできます。Agent Merge を有効にする前に、作業をレビューし、準備ができていることを確認してください。

## Agent Merge で PR を管理する

作成したコードをレビューして PR を作成し、残りのプロセスを Agent Merge に任せます。

1. Codespace に戻ります。
2. `/agent` を入力してエージェントのダイアログを開きます。
3. 一覧から **Default** を選び、<kbd>Enter</kbd> を押します。
4. `/pr create` コマンドで新しい PR を作成します。
5. `/pr agentmerge` で Agent Merge を有効にします。
6. Copilot は PR の継続的インテグレーションを確認します。すべて成功するとマージを実行します。
7. `PR #14 was squash-merged successfully.` のような Copilot のメッセージが表示されたことを確認します。

> [!IMPORTANT]
> Agent Merge は、必須の承認、ブランチ保護、マージキュー、リポジトリの設定、権限不足を回避しません。ブロックされた場合は理由を確認し、リポジトリで許可されている通常の手順でレビュー済みの変更を手動マージしてください。

## まとめと次のステップ

コードの生成、テストと検証、プルリクエストの処理など、開発プロセスの一部を自動化しました。このレッスンでは、Agent Merge の仕組みを学び、機能全体と検証の証拠を確認し、フィルター PR を作成して、レビュー後に Agent Merge を有効にしました。

次は、コンテキスト、モデル、共有、任意のクラウド委任に使える[便利な Copilot CLI のスラッシュコマンド][next-lesson]を確認します。

## リソース

- [Copilot CLI でプルリクエストを管理する][manage-prs]
- [Copilot CLI コマンドリファレンス][cli-reference]

[previous-lesson]: ../7-qa-agent/
[next-lesson]: ../9-cli-power-tools/
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
