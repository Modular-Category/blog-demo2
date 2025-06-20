# QWorld 解説サイトのアーキテクチャ設計
Docusaurusの機能を最大限に活用し、qworld.pdf の静的なドキュメントを超える、動的で理解しやすい解説サイトを構築するためのアーキテクチャを提案します。

1. 基本方針
情報の階層化: ユーザーが目的の情報にたどり着きやすいように、「チュートリアル」「概念ガイド」「APIリファレンス」のように情報を分類・階層化します。

インタラクティブ性: MDX (Markdown + JSX) を活用し、コードスニペットと生成される図を並べて表示することで、直感的な理解を促します。

多言語対応 (i18n): Docusaurusの国際化機能を用いて、日本語版と英語版のサイトをスムーズに切り替えられるようにします。

検索性: Algolia DocSearchを導入し、膨大なコマンドやオプションの中からでも目的の機能を見つけやすくします。

2. サイト全体の構造
Docusaurusの標準的なディレクトリ構造に沿って、以下のようにコンテンツを配置します。

qworld-docs/
├── docs/                # メインのドキュメント（使い方、ガイド、リファレンス）
│   ├── introduction/
│   ├── tutorials/
│   ├── guides/
│   └── reference/
├── blog/                # お知らせ、アップデート情報、応用例など
├── src/
│   ├── components/      # カスタムReactコンポーネント（例：コードと図のプレビュー）
│   └── pages/           # トップページなどのスタンドアロンページ
│       └── index.js
├── i18n/
│   └── en/              # 英語翻訳ファイル
│       ├── docusaurus-plugin-content-docs/
│       │   └── current/ # 英語のdocsコンテンツ
│       └── ...
└── docusaurus.config.js # Docusaurus全体の設定ファイル

3. コンテンツの階層構造（docs/内）
qworld.pdf の構成を参考にしつつ、ウェブサイトとしてより分かりやすいように再構成します。

カテゴリ1: はじめに (Introduction)
ユーザーが最初に訪れる場所です。QWorld の概要を掴み、使い始めるまでのハードルを下げます。

_category_.json: "はじめに"

what-is-qworld.md: QWorld とは何か？（概要、特徴、解決する課題）

installation.md: インストール方法（CTAN, TeX Live）

quick-start.md: 5分でわかるQWorld（基本的な図を描画する最短のチュートリアル）

カテゴリ2: チュートリアル (Tutorials)
基本的な使い方をステップ・バイ・ステップで学びます。

_category_.json: "チュートリアル"

basic-elements.md: 基本要素 (\q, \qbox, \qwire, \qcirc)

customizing-boxes.md: ボックスのカスタマイズ (n, s, hlen, vlen など)

layout-and-positioning.md: レイアウトと座標 (\qmv, at, \n など)

colors-and-styles.md: 色とスタイルの適用 (color, arrowtype など)

カテゴリ3: 概念ガイド (Guides)
qworld.pdf の第4章に相当する部分です。圏論の概念と、それを QWorld でどう表現するかを関連付けて解説します。

_category_.json: "概念ガイド"

monoidal-categories.md: モノイダル圏

braiding.md: 組紐（六角形等式、ヤン・バクスター方程式）

duality.md: 双対性（ジグザグ等式、コピー禁止定理）

pivotal-and-ribbon.md: ピボタル圏とリボン圏

dagger-categories.md: ダガー圏

カテゴリ4: APIリファレンス (API Reference)
全コマンドとオプションを網羅した辞書的なセクションです。

_category_.json: "APIリファレンス"

elements/:

qbox.md: \qbox コマンドの詳細

qwire.md: \qwire コマンドの詳細

qbraid.md: \qbraid, \qbraidinv, \qsym

qcap-qcup.md: \qcap, \qcup

... (その他のコマンドも同様にファイルを作成)

options.md: 全コマンド共通のオプション（id, show idなど）

4. トップページの設計 (src/pages/index.js)
サイトの「顔」となるページです。以下の要素を配置し、QWorld の魅力を伝えます。

ヒーローセクション:

キャッチーな見出し：「圏論の図式計算を、もっと直感的に、美しく。」

QWorld の簡潔な説明。

qworld.pdf の図6や図39のような、カラフルで視覚的に訴える図版をヒーローイメージとして使用。

特徴紹介:

「シンプルな構文」「TikZによる高いカスタマイズ性」「豊富な圏論的構造のサポート」など、主要な特徴を3〜4点、アイコンと共に紹介。

コールトゥアクション (CTA):

「インストール」や「チュートリアルを始める」への導線となるボタンを配置。

作例ギャラリー:

qworld.pdf から選りすぐりの美しい図版をいくつか並べ、クリックするとコードと解説が見られるようにする。

5. インタラクティブ性の向上 (MDXの活用)
各ページのコード例を、単なるテキストではなく、以下のようなカスタムReactコンポーネントで表示することを強く推奨します。

CodePreview コンポーネントのイメージ:

<CodePreview
  title="フロベニウス則"
  latexCode={`
\\q{
  \\qwire\\qmul[hlen=2,color=black]\\n
  \\qcomul[hlen=2]\\qwire
} = \\q{
  \\qcomul[hlen=2]\\n
  \\qmul[hlen=2,color=black]
}
  `}
  imageUrl="/img/examples/frobenius-law.svg"
/>

これにより、コードとその出力結果を並べて表示でき、ユーザーは結果を視覚的に確認しながら学習を進められます。（将来的には、WebAssembly上でLaTeXを動かし、リアルタイムでプレビューを生成するプレイグラウンドに拡張することも考えられます。）