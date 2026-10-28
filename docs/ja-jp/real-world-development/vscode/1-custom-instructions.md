---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Agent mode →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクの進め方や、Copilot が知っておくべき背景情報を伝える方法はいくつかあります。特に重要なのが[指示ファイル][instruction-files]です。指示ファイルを使うと、コード生成の指針を Copilot に伝えられます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパスに応じた指示ファイルを通じて、プロジェクトのコンテキスト、コーディングガイドライン、ドキュメントの標準が Copilot に伝わる方法を確認します。
- 現在の指示を適用した状態で、フィルター機能の最初のデータ部分 (publishers ヘルパー) を生成します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加します。
- 同じプロンプトをもう一度実行し、生成コードが新しい標準に従うことを確認します。
- 次の演習で Copilot が最新の指示を使えるよう、指示の変更とフィルター機能の一部を `main` にコミットします。

> [!CAUTION]
> 生成コードは、一部の標準に従わない場合があります。Copilot の動作は確率的です。この演習では、指示を更新した後の動作の**傾向**を確認します。生成結果が文字単位ですべて一致することを目指すものではありません。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発手法に関するガイドラインや要件があります。

- データ層には常にユニットテストが必要です。
- UI はダークモードで、現代的な見た目にする必要があります。
- コードには TSDoc doc comment を使ってドキュメントを追加します。
- 各ファイルの先頭に、ファイルの役割を説明するコメントブロックを追加します。

指示ファイルを使って、Copilot がこれらの開発手法に沿ってタスクを実行できるようにします。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件について Copilot にコンテキストと希望を伝え、より適切な提案やコードを生成できるようにします。コーディング規約、ライブラリ、含めたいコメントの種類などを指定できます。リポジトリ全体に適用する指示も、特定のファイルやタスクに適用する指示も作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md`: リポジトリに関するすべてのリクエストで Copilot に送信される単一の指示ファイルです。技術スタック、開発対象の概要、ベストプラクティスなど、ほとんどのチャットや CLI のリクエストに関係するプロジェクト全体の情報を記載します。
- `.github/instructions/*.instructions.md`: 特定のタスクやファイルの種類に使う指示ファイルです。TypeScript などの言語や、UI コンポーネント、ユニットテストの作成などのタスクに対するガイドラインを記載できます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat でのコード生成にのみ使われます。コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、リポジトリレベルの指示ファイルと、`applyTo` frontmatter がある `*.instructions.md` ファイルの両方を使ってコードを生成します。
>
> Copilot は [AGENTS.md や CLAUDE.md など][custom-instructions-support]、ほかの標準の指示ファイルにも対応しています。

### 指示ファイルを管理するためのベストプラクティス

この演習では指示ファイルの作成方法を詳しく扱いませんが、サンプルプロジェクトには代表的な構成例があります。基本的には、次の点を意識してください。

- `copilot-instructions.md` には、開発対象の説明、プロジェクトの構造、全体に適用するコーディング標準など、プロジェクト全体に関する指示を記載します。
- `*.instructions.md` には、ユニットテスト、Astro コンポーネント、データ層など、特定のファイルやタスクに関する指示を記載します。
- 自然な言葉を使い、明確なガイダンスとコードの例を示します。

指示ファイルの作成方法は 1 つではありません。AI のほかの使い方と同様に、プロジェクトに合う方法を試してください。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、十分な指示ファイルを用意してください。このプロジェクトには、[UI 更新][ui-instructions]や [Astro][astro-instructions] など、さまざまなタスクに対応する指示ファイルがあります。
>
> Copilot に指示ファイルを生成してもらうこともできます。各サーフェスで方法が異なります (例: VS Code の **Configure Chat → Generate Agent Instructions**、Copilot CLI の `/init`)。必要な場合は、利用中のサーフェスのレッスンで説明します。
>
> テンプレートや出発点を探す場合は、指示ファイル、カスタムエージェント、その他のリソースが集まった [awesome-copilot][awesome-copilot] を参照してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization

## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリに含まれる指示ファイルを読みます。中心となる `copilot-instructions.md` と、さまざまなタスクに対応する `*.instructions.md` ファイルがあります。エディターまたは GitHub Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の下には **GitHub Actions Workflows** の指針があります。
3. `.github/instructions` フォルダーを開き、内容を確認します。Astro ファイル、Drizzle データ層、テストなどの指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開きます。`applyTo` フィールドが、適用対象のファイルをリポジトリルート相対の glob で指定します。ここでは、`**/*.test.ts` に一致する TypeScript テストファイルが対象です。
5. このプロジェクトのユニットテスト作成に関する指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。別の指示ファイル (例: `unit-tests.instructions.md`) や、プロジェクト内の既存ファイルへのリンクを確認します。大きな指示を再利用できる小さなファイルに分け、Copilot が参考にできる例を示せます。そこに記載されたパスは指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはプロジェクトのコーディング標準が記載されていますが、コード内のドキュメントはまだ必須ではありません。次の手順で TSDoc doc comment とファイル先頭のコメントヘッダーに関するルールを追加します。

## 変更作業用のブランチを作成する

ベストプラクティスに従い、変更作業用のブランチを作成します。

1. 前の演習で使った Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
3. 新しいブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b custom-instructions
   ```

## 指示を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めてください。モードとモデルの選択は演習ごとに異なります。必要な場合は手順で説明します。

カスタム指示の効果を確認するため、まず現在の指示ファイルを使ってプロンプトを送信します。後で指示を更新し、同じプロンプトをもう一度送って違いを確認します。

1. 前の演習で開いたエディタータブを閉じ、Copilot が必要なコンテキストだけを取得できるようにします。
2. `src/lib/publishers.ts` を開き、ヘルパーを置く場所を Copilot に伝えます。
3. Chat ビューのエージェントドロップダウンで **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot はプロジェクトを調べ、ヘルパーファイルやテストなどのコードを更新します。
6. 提案されたヘルパーが、最初の引数に `db` クライアントを取り、型付きの出版社配列を返す関数であることを確認します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` の規約に従っています。
7. 一方で、提案されたコードには TSDoc doc comment とファイル先頭のコメントヘッダーがありません。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくても doc comment を追加する場合があります。その場合も問題ありません。指示を更新した後に動作が一貫することが、この演習の目的です。

## リポジトリ全体に適用する標準を追加する

`.github/copilot-instructions.md` は Copilot にプロジェクト全体の情報を提供するファイルです。コード提案の品質を高めるため、コーディング標準を追加します。

1. `.github/copilot-instructions.md` を再度開きます。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。既存の標準には、インラインドキュメントのルールがありません。
3. 既存の標準の直後に、次の Markdown を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 指示ファイルは、リポジトリレベル (`.github/copilot-instructions.md`) に置いて全体に適用することも、言語やファイルの種類、特定のタスクに対する `*.instructions.md` として作成することもできます。ここで追加した doc comment のルールのようなプロジェクト全体の標準は、リポジトリレベルのファイルに記載します。

## 同じプロンプトをもう一度送信して違いを確認する

1. Copilot Chat に戻り、**New Chat** を選択して会話を新しくします。
2. `src/lib/publishers.ts` を選択し、Copilot が適切なファイルに注目していることを確認します。
3. 先ほどと**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの先頭に、次のようなコメントブロックが追加されていることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 関数に次のような TSDoc comment が含まれていることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

Copilot を誘導して新しいプロジェクト標準に従わせ、次の演習で利用する実際のコードに適用できました。

## 指示とブランチをコミットしてプッシュする

指示ファイルもほかのリポジトリアセットと同様に、ソース管理で管理します。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
2. 指示の更新とヘルパーの変更を確認します。

   ```bash
   git status
   ```

3. 指示の更新と、ヘルパーの基盤として Copilot が変更したファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # If git status shows additional supporting updates (for example tests), add those files too.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、バックログのドキュメント標準に関する Issue に関連付けたプルリクエストを作成します。GitHub ツールはすでにプロジェクトに接続されているため、Copilot に Issue を見つけて PR を作成し、マージしてもらいます。

> [!NOTE]
> Copilot が GitHub 上の操作を行う最初の演習です。プロジェクトテンプレートには GitHub ツールがあらかじめ設定されています。Copilot が初めてツールを使うとき、VS Code から GitHub へのサインインを求められることがあります。案内に従って認証してください。この接続方式 (Model Context Protocol) については、後の演習で学びます。

1. Codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント選択で **Agent** を選びます。
3. 現在のブランチから PR を作成し、指示の更新と publishers ヘルパーの基盤を説明して、関連 Issue を閉じるよう Copilot に依頼します。

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot に GitHub 操作の許可を求められたら、コマンドを確認して必要に応じて **Allow** を選択します。
5. PR が作成されたら、次のプロンプトでマージしてブランチを `main` に戻すよう依頼します。

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

6. GitHub の操作やシェルコマンドの実行を許可するよう求められた場合は、内容を確認して適切に **Allow** を選択します。

これで GitHub Copilot の支援を受けてプルリクエストを作成し、マージできました。

## まとめと次のステップ

Copilot が指示ファイルからコンテキストを取得する方法を確認し、VS Code の Copilot Chat を使って次のことを行いました。

- コード生成プロンプトを送信し、既存の指示に基づく提案を確認しました。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加しました。
- 同じプロンプトを再実行し、提案コードが新しい標準に従うことを確認しました。
- 指示の更新とヘルパーの基盤を `main` にコミットし、次の演習で使えるようにしました。

次は [agent mode][next-lesson]を使い、コードベース全体に機能を追加するときに指示を活用します。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示の作成に関するベストプラクティス][instructions-best-practices]
- [よりよい Copilot のカスタム指示を書くための 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム指示][personal-instructions]
- [指示ファイルなどのリソース集: Awesome Copilot][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
