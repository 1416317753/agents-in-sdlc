---
title: "レッスン 1 - GitHub Copilot CLI のインストール"
description: "Codespace に Copilot CLI をインストールして認証し、基本を確認して、登録済みのフィルター Issue を見つけます。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

[GitHub Copilot CLI][about-copilot-cli] は、ターミナルで動作するエージェント型コーディングアシスタントです。コードベースを探索し、コードを生成し、コマンドを実行し、外部ツールとやり取りできます。作業を任せて変更を依頼しながら、ターミナルでの作業を続けられます。まずはツールをインストールしましょう。

このレッスンでは、次のことを行います。

- npm で GitHub Copilot CLI をインストールします。
- GitHub アカウントで認証します。
- ワークショップのリポジトリを信頼し、簡単な会話で動作を確認します。
- 組み込みの GitHub MCP server でフィルターの Issue を見つけます。

## シナリオ

チームでは、増え続けるバックログに AI エージェントを活用し始めています。Copilot CLI を使うと、開発者が普段使っているターミナルでその機能を利用できます。このレッスンでは、残りのワークショップに備えてインストールと認証を行います。

## Copilot CLI をインストールする

Copilot CLI は [npm][install-cli]、WinGet、Homebrew からインストールできます。GitHub Codespaces には Node.js があらかじめインストールされているため、ここでは npm を使います。

1. Codespace に戻り、ターミナルを開きます。
2. Node.js がインストール済みで、必要なバージョンを満たすことを確認します。

   ```bash
   node --version
   ```

   バージョン 24 以降が表示されます。

3. Copilot CLI をグローバルにインストールします。

   ```bash
   npm install -g @github/copilot
   ```

4. インストールを確認します。

   ```bash
   copilot --version
   ```

   バージョン番号が表示されます。

## GitHub で認証する

初回起動時に、Copilot CLI から GitHub アカウントでの認証を求められます。

1. Copilot CLI を起動します。

   ```bash
   copilot
   ```

2. 求められたら、デバイスコードの手順に従って認証し、Copilot CLI を承認します。
3. Copilot CLI に次の確認が表示されます。

   ```plaintext
   Copilot can read files in this folder and, with your permission, edit them or run code and shell commands. It will remember your permissions for the rest of this session.

   Do you trust the files in this folder?
   ```

4. パスが Tailspin Toys リポジトリであることを確認し、**Yes, and remember this folder for future sessions** を選択して信頼します。

> [!NOTE]
> Codespace では、GitHub セッションを通じてすでに認証されている場合があります。認証の確認がなく Copilot CLI が起動した場合は、そのまま利用できます。

## 基本を確認する

通常のシェルプロンプトでは Codespace 上で直接コマンドを実行できます。Copilot CLI の起動後は、自然言語をエージェントに送り、スラッシュコマンドで会話を操作します。

1. `/model` を入力し、矢印キーで **Auto** を選択して <kbd>Enter</kbd> を押し、もう一度 <kbd>Enter</kbd> を押して確定します。
2. `/help` を入力して、インストール済みバージョンのコマンド一覧を確認します。<kbd>Esc</kbd> を押してヘルプ画面を閉じます。
3. 動作確認のため、Copilot に簡単な質問をします。

   ```plaintext
   What are the key files in this project?
   ```

4. 応答を読み、Copilot が回答前にリポジトリを調べることを確認します。
5. `/mcp list` を入力し、組み込みの GitHub MCP server が利用可能であることを確認します。
6. 次のプロンプトでフィルターの Issue を探します。

   ```plaintext
   Using GitHub MCP, find the issue in this repository titled "Allow users to filter games by category and publisher." Give me its URL and a short summary. Don't change anything.
   ```

7. URL を開いて Issue を読みます。最初の変更を行うときに使用します。

> [!TIP]
> 通常の Copilot CLI セッションは、ターミナルでチェックアウト中のブランチを使います。自動的に worktree は作成されません。変更ごとに機能ブランチを作成します。

## ワークショップ用のショートカットを使う

Copilot CLI は、許可された操作以外のツールを使う前に通常は確認します。このワークショップでは `--yolo` を使って再起動します。このオプションは Codespace 内で承認確認を省略し、演習に集中できるようにします。

> [!CAUTION]
> `--yolo` は、`--allow-all-tools`、`--allow-all-paths`、`--allow-all-urls` を含む完全な自動許可を有効にします。Codespace や VM などの隔離環境でのみ使い、日常の開発で既定の設定としてエイリアスにしないでください。詳細は[ツールの使用を許可または禁止する方法][allow-all-warning]を参照してください。

このワークショップでは、後のレッスンで Issue やプルリクエストを操作するため、`--enable-all-github-mcp-tools` で読み書き可能な GitHub MCP ツールも有効にします。Codespace はローカルコンピューターへのアクセスを制限しますが、認証済みの GitHub リソースは実際のものです。公開やマージの前に変更をレビューしてください。

1. `/exit` で Copilot CLI を終了します。
2. リポジトリのルートから再起動します。

   ```bash
   copilot --yolo --enable-all-github-mcp-tools
   ```

3. プロジェクトについて簡単な質問を送り、会話が動作することを確認してから `/exit` で終了します。

Copilot は会話を自動的に保存します。後で指示を変更したりエージェントを追加したりしたら、`copilot --resume` で同じ機能の会話とブランチに戻ります。

## まとめと次のステップ

このレッスンでは、次のことを行いました。

- npm で GitHub Copilot CLI をインストールしました。
- GitHub アカウントで認証しました。
- ワークショップのリポジトリを信頼し、会話で動作を確認しました。
- 組み込みの GitHub MCP server でフィルターの Issue を見つけました。

次は[最初の小さな変更][next-lesson]に取り組み、ゲームカードに星評価を表示します。

## リソース

- [GitHub Copilot CLI のインストール][install-cli]
- [GitHub Copilot CLI について][about-copilot-cli]
- [Copilot CLI コマンドリファレンス][cli-reference]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-add-star-rating/
[install-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli
[about-copilot-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[allow-all-warning]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli#allowing-and-denying-tool-use
