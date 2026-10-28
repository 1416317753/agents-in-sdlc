---
title: "演習 1 - カスタム指示 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で実行する必要がある場合や、Copilot が知っておくべき背景情報がある場合、その情報にアクセスできるようにします。[指示ファイル][instruction-files]を使うと、Copilot に「何を」するかだけでなく、「どのように」作業するかも伝えられます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパスに応じた指示ファイルを通じて、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメントの標準が Copilot に伝わる方法を確認します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加します。

> [!NOTE]
> VS Code や CLI のハーネスとは異なり、この演習では変更前後のプロンプトを比較しません。Copilot cloud agent は GitHub Issue に対して非同期に作業するため、リアルタイムで並べて効果を確認することが難しいためです。このハーネスの後半で cloud agent が作成したプルリクエストをレビューするときに、指示ファイルの影響を確認します。

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
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses

## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリに含まれる指示ファイルを読みます。中心となる `copilot-instructions.md` と、さまざまなタスクに対応する `*.instructions.md` ファイルがあります。エディターまたは GitHub Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の下には **GitHub Actions Workflows** の指針があります。これらの指示は Copilot を使うあらゆる作業に適用されます。
3. `.github/instructions` フォルダーを開き、内容を確認します。Astro ファイル、Drizzle データ層、テストなどの指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、適用対象のファイルをリポジトリルート相対の glob で指定します。ここでは、`**/*.test.ts` に一致する TypeScript テストファイルが対象です。
5. このプロジェクトのユニットテスト作成に関する指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの別の指示ファイルや、プロジェクト内の既存ファイルへのリンクを確認します。大きな指示を再利用できる小さなファイルに分け、Copilot が参考にできる例を示せます (ここに記載されたパスはリポジトリルートではなく、指示ファイルからの相対パスです)。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはプロジェクトのコーディング標準が記載されていますが、コード内のドキュメントはまだ必須ではありません。次の手順で TSDoc doc comment とファイル先頭のコメントヘッダーに関するルールを追加します。

## 新しい標準を追加する

`.github/copilot-instructions.md` は Copilot にプロジェクト全体の情報を提供するファイルです。コード提案の品質を高めるため、リポジトリのコーディング標準を追加します。

ここで行う編集は、TSDoc doc comment とファイルレベルのコメントヘッダーに関するプロジェクト全体のルールを追加することです。編集する前に、作業用のブランチを作成します。cloud agent は Issue の対象ブランチから指示を読み込みますが、変更をコミットするためにもブランチを用意します。

1. Codespace のターミナルで、次のコマンドを実行してブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

2. `.github/copilot-instructions.md` を再度開きます。
3. 27 行目付近にある **Code formatting requirements** セクションを探します。プロジェクトのコーディング標準は記載されていますが、インラインドキュメントのルールがありません。
4. 既存の標準の直後に、次の Markdown を追加します。これにより、ファイル先頭のコメントヘッダーと TSDoc doc comment を Copilot に要求します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

5. `copilot-instructions.md` を保存します。

> [!TIP]
> 指示ファイルは、リポジトリレベル (`.github/copilot-instructions.md`) に置いて全体に適用することも、言語やファイルの種類、特定のタスクに対する `*.instructions.md` として作成することもできます。ここで追加した doc comment のルールのようなプロジェクト全体の標準は、リポジトリレベルのファイルに記載します。

## 指示の変更をコミット、プッシュ、マージする

cloud agent は、Issue が対象とするブランチの指示ファイルを読み込みます。次の演習で Copilot に Issue を割り当てると、`main` からブランチを作成します。そのため、cloud agent がこの指示を利用できるよう、変更を `main` に反映します。

1. ステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを作成してマージします。次の演習で Issue を割り当てるとき、cloud agent はこの指示を読み込みます。

> [!TIP]
> このワークショップでは `main` に直接コミットしてもかまいません。その場合はブランチの作成手順を省略できます。実際のプロジェクトでの手順に近づけるため、ここではブランチを使っています。

## まとめと次のステップ

このプロジェクトで Copilot が指示ファイルからコンテキストを取得する方法を確認し、`.github/copilot-instructions.md` にリポジトリ全体へ適用する標準を追加しました。このハーネスの後半で cloud agent が作成するプルリクエストをレビューするときに、この標準が反映されていることを確認します。

次は、Copilot cloud agent に最初の Issue を[割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示の作成に関するベストプラクティス][instructions-best-practices]
- [よりよい Copilot のカスタム指示を書くための 5 つのヒント][copilot-instructions-five-tips]
- [指示ファイルなどのリソース集: Awesome Copilot][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
