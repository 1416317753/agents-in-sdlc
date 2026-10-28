---
title: "演習 5: GitHub Copilot の作業を反復して改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業内容をレビューする

このラボでは、ユーザー体験の改善と機能追加を目的とする複数のタスクを GitHub Copilot に依頼してきました。コードへのドキュメント追加、デザインチームが試せる関連ゲーム機能の作成、ハイコントラストとライトモードによるアクセシビリティ機能の実装です。コード変更を確認し、必要であれば Copilot にフィードバックを送って改善しましょう。

### シナリオ

生成 AI を利用しても、ソフトウェア設計と DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスに沿って作業します。チーム全体にレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能に関する GitHub Copilot の提案を確認します。

## GitHub Copilot cloud agent のセキュリティ

Copilot cloud agent は非同期に、監視を受けずにタスクを実行するため、安全性を保つための制約があります。

- Copilot はリポジトリへの読み取りアクセス権を持ち、書き込みできるのは作業に使うブランチだけです。
- Cloud agent は GitHub Actions 内で、作業用の独立した一時環境を作成して実行されます。
- GitHub Actions ワークフローを実行するには、人間による承認が必要です。
- MCP server などの[外部リソースへのアクセスは既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。

## 生成されたドキュメントをレビューする

GitHub Copilot が生成した最初のプルリクエスト (PR)、つまりコードへのドキュメント追加をレビューします。github.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR に、GitHub Copilot がファイアウォールでブロックされているという警告が表示されることがあります。Copilot は MCP server などの外部リソースへのアクセスが既定で制限されているため、これは**想定どおり**です。必要であれば、[Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻り、**Pull Requests** を選択します。
2. **Add missing documentation** などのタイトルのプルリクエストを開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストに **[WIP]** が付いています。Copilot が作業を完了するまで待ってください。数分かかることがあります。

3. 準備ができたら **Files changed** タブを選び、変更を確認します。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

4. 新しく追加された TSDoc doc comment などのドキュメントを確認します。具体的な変更内容は状況によって異なります。演習 2 の開始時に確認したカスタム指示ファイルによって追加された TSDoc doc comment、TypeScript 規約、コメントヘッダーに注目してください。
5. 問題がなければ **Conversation** タブに戻り、下にスクロールします。
6. ワークフローの承認待ちを示す表示がある場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

7. PR の checks セクションでワークフローがキューに追加されます。問題がなければ、単一の Astro アプリのプロジェクトチェックが成功します。完了まで数分かかることがあります。

## GitHub Copilot に変更を依頼する

プルリクエストで Copilot とやり取りすることは一方通行ではありません。チームメンバーと同じように、プルリクエストやコードのインラインコメントで Copilot にメンションできます。Copilot はコメントを確認し、別のセッションを開始して対応します。結果は予測できないため、必ず成功する指示文はありません。次のような変更を依頼できます。

- 各コードファイルの先頭に、役割を説明するコメントヘッダーを追加する。
- TypeScript と Astro ファイルに TSDoc doc comment を追加する。
- Astro アプリの構造を説明する README を作成する。

1. **@copilot** をメンションし、生成されたドキュメントの変更を依頼するコメントを追加します。上記の例を使うか、コードベースのドキュメントについて別の変更を依頼します。
2. **View Session** を選択して Copilot の作業を確認します。Copilot が更新用の新しいセッションを開始します。
3. **Back to pull request** を選択すると、プルリクエストに戻れます。

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot の作業が完了すると、新しいコミットがプルリクエストに追加されます。
5. **Files changed** タブを選択し、変更を確認します。

納得できるまで反復して改善してください。問題がなければ、ドラフト PR をレビュー可能な状態にして `main` にマージできます。

![Convert PR to ready](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

ゲーム詳細ページへの関連ゲームの表示について、Copilot が作成した PR を確認します。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択し、**Show related games on the game details page** などのタイトルの PR を開きます。
3. **Files changed** タブで生成されたコードを確認します。
4. 問題がなければ **Conversation** タブに戻り、ワークフローの承認待ちが表示されているか確認します。表示されている場合は **Approve and run workflows** を選択します。
5. PR の checks セクションでワークフローの結果を確認します。単一の Astro アプリのプロジェクトチェックが成功するまで数分かかることがあります。
6. **オプション:** Codespace でこのブランチに切り替えて、関連ゲーム機能を手動テストできます。Copilot が作成したブランチ名を `<branch-name>` に指定して、次のコマンドを実行します (例: `copilot/fix-8`)。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

関連ゲーム機能ができました。Copilot cloud agent に新しいコメントを追加すると、機能に表示するゲームの数の変更や、コメントヘッダーと TSDoc doc comment の追加などを依頼できます。この Issue は指示を追加する前に割り当てたため、必要であれば改めて依頼してください。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティカスタムエージェントが実装した機能をレビューします。この PR には、演習 3 で依頼したハイコントラストモードと、演習 4 でミッションコントロールから追加依頼したライトモードの両方が含まれているはずです。

1. GitHub.com のリポジトリに戻り、**Pull Requests** タブを選択します。
2. **Add high contrast mode to website** などのタイトルの PR を開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、PR に **[WIP]** が付いています。完了するまで待ってください。数分かかることがあります。

3. **Files changed** タブを選択してコードを確認します。特に次の点を確認してください。
   - モード切り替え用の UI コンポーネント
   - ユーザー設定を保存する local storage
   - ハイコントラストモードとライトモードの CSS またはスタイルの変更
   - アクセシビリティ属性 (ARIA ラベル、キーボード操作など)
   - モード切り替えを管理する JavaScript/TypeScript コード
4. 問題がなければ **Conversation** タブに戻り、ワークフローの承認待ちが表示されている場合は **Approve and run workflows** を選択します。
5. PR の checks セクションでワークフローの結果を確認します。
6. **オプション:** Codespace で Copilot が作成したブランチに切り替え、アクセシビリティ機能を手動テストできます。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    アプリを起動し、ブラウザーでハイコントラストモードとライトモードの切り替えをテストします。ページを再読み込みしても設定が保持されることを確認してください。

アクセシビリティエージェントが、ベストプラクティスに沿った実装を Copilot に促したことが分かります。アクセシビリティに関する懸念や改善点があれば、前の PR と同様にコメントで **@copilot** に更新を依頼できます。

## オプションの演習 — 委任を続ける

実際のバックログ作業をエージェントに任せ、自分は別のタスクに取り組めるとき、cloud agent の効果が発揮されます。練習としてリポジトリにいくつか Issue を作成し、Copilot に割り当ててください。次のような例があります。

- ゲーム詳細ページに支援者向け問い合わせフォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセスヘルパーに入力検証とエラー処理を追加する。

## まとめ

Cloud agent ハーネスを完了しました。これらのレッスンを通じて、次のことを行いました。

- このリポジトリに含まれるカスタム指示ファイルを確認し、cloud agent の出力への影響を把握しました。
- Issue を Copilot cloud agent に割り当て、環境の準備、計画、非同期の実行を確認しました。
- アクセシビリティ用のカスタムエージェントを作成して使用し、ハイコントラストとライトモードの切り替えを追加しました。
- Copilot agents ページをミッションコントロールとして使い、進行中のアクセシビリティセッションを監視して案内しました。
- Copilot のプルリクエストをレビューして反復し、`@copilot` に変更を依頼してワークフローを承認しました。

## レビューと次のステップ

Cloud agent ハーネスを完了しました。ほかのハーネスを試すと、ここで学んだことをさらに活用できます。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE で Copilot Chat の agent mode と MCP 統合を試します。
- 💻 **[CLI ハーネス](../../cli/)** — Plan と Autopilot モード、エージェントスキル、QA カスタムエージェント、Playwright MCP、Agent Merge、cloud agent に戻る `/delegate` を使い、ターミナルからレビュー済みの変更を 2 つ届けます。

自分のリポジトリでは、次のアイデアも試せます。

- リファクタリングやテストカバレッジの Issue を cloud agent に割り当てる。
- 別の分野のカスタムエージェントを作成する。
- 別の技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot agents について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローに関するベストプラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールの設定][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
