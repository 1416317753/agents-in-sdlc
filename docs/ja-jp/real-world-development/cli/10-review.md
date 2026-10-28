---
title: "レッスン 10 - 振り返りと次のステップ"
description: "Copilot CLI のワークフロー、2 つのプルリクエスト、再利用できるカスタマイズ、追加リソースを振り返ります。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

GitHub Copilot CLI を使い、Tailspin Toys の一連のワークフローを進めました。次のことを行いました。

- Codespace を準備し、Copilot CLI をインストールして、プロジェクトを確認し、登録済みのフィルター Issue を見つけました。
- 星評価を追加し、転送したブラウザーで結果を確認して、最初のプルリクエスト (PR) を手動でマージしました。
- フィルター Issue から始め、Plan モードで方針を決め、Autopilot モードで実装し、Interactive モードでレビューしました。
- カスタム指示でエージェントを導き、既存の `quality-checks` スキルをカスタマイズして、ユニットテスト、lint、型チェックを実行しました。
- Playwright Model Context Protocol (MCP) server を追加し、実際のブラウザーでフィルター機能を確認しました。
- 品質保証 (QA) カスタムエージェントを作成して選択し、要件、カバレッジ、スキルの結果、ブラウザーでの証拠を確認しました。
- フィルター機能全体をレビューし、Agent Merge を承認して PR を処理しました。
- コンテキスト、モデル、共有、cloud agent への任意の委任に使うスラッシュコマンドを確認しました。

## リリースしたもの

このワークショップには、更新済みの `main` からそれぞれ作成する 2 つの PR があります。

1. **星評価**: 既存の `starRating` と未評価の場合の表示をゲームカードに追加します。
2. **フィルターと品質ワークフロー**: フィルター機能を実装し、指示を更新して機能に適用し、`quality-checks` レポートをカスタマイズし、QA プロファイルを作成して、関連テストを追加します。

フィルター機能の計画から PR 作成まで、同じ会話とブランチを使用しました。ワークショップを簡潔にするため、作業を 1 つの PR にまとめています。

## 検証方法の違い

自動テスト、自分でのブラウザー確認、MCP を介した Copilot によるブラウザー確認など、複数の方法でコードを確認しました。`quality-checks` スキルは、ユニットテスト、lint、型チェックを実行し、追加した形式で結果を報告します。QA エージェントは、要件とテストカバレッジをレビューして、それらの結果をブラウザーでの証拠と合わせました。

追加のテストは、実際に不足しているカバレッジを補うものである必要があります。QA の実行で追加テストが不要と判断される場合もあります。マージを承認する前に、コードと証拠を確認してください。変更があった場合は、影響を受ける証拠も更新します。

## ベストプラクティス

Copilot に与えるコンテキストとツールが、作業結果に影響します。このワークショップでは、指示を更新し、スキルをカスタマイズし、QA プロファイルを作成し、MCP server を設定しました。会話をまたいでこれらのカスタマイズを再利用し、チームのニーズに合わせて調整できます。指示は標準を設定し、スキルは繰り返し実行するタスクを説明し、カスタムエージェントは専門家の役割を定義し、MCP server は外部ツールに接続します。エージェントの要約だけでなく、実際の変更とツールの結果を確認してください。

タスクに合った**モードとモデル**を選びます。**Plan** は実装前に方針を検討するとき、**Interactive** は対象を絞った変更を対話しながら進めるとき、**Autopilot** は範囲が明確なタスクに使います。定型的な編集には高速なモデルを、複雑な作業にはより高性能なモデルを選択します。

インフラと同じくらいコンテキストも重要です。何を作るのか、なぜ必要か、どのように実装するかを明確に説明すると、結果が大きく変わります。

## さらに試す

基本的なワークフローを学びました。Copilot CLI の機能をさらに確認するには、次のコマンドも使えます。

- `/review` でコードレビューエージェントに変更を分析してもらいます。
- `/rubber-duck` で問題を説明し、別の視点を得ます。
- `/fleet` で独立したサブタスクを並列に調整します。
- `/worktree` で別のタスクを分離します。
- `/delegate` で Copilot cloud agent にタスクを送信します。

## 次のステップ

どのツールも、使い続けることが上達につながります。本番コード、趣味のコード、何年も作りたいと思っていた小さなアプリなどで試してください。学んだことをチームで共有し、ほかの人からも学びましょう。ドキュメントも確認してください。

ターミナルから Tailspin Toys を拡張する場合は、オプションの [Foundry Backer Concierge シリーズ][foundry]に進んでください。別の環境を比較する場合は、[VS Code ワークショップ][vscode]、[GitHub Copilot app ワークショップ][app]、または [Copilot cloud agent ワークショップ][cloud]を参照してください。

## リソース

- [GitHub Copilot CLI について][about-cli]
- [Copilot CLI コマンドリファレンス][cli-reference]
- [Copilot CLI のカスタマイズ][customize-cli]
- [Copilot CLI でプルリクエストを管理する][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
