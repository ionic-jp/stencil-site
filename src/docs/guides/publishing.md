---
title: Publishing A Component Library
description: Publishing A Component Library
url: /docs/publishing
contributors:
  - adamdbradley
---

# コンポーネントライブラリの公開

コンポーネントライブラリを公開して配布し、外部プロジェクトで使用するための戦略は数多くあります。 ステンシルの利点の1つは、ユースケースに適したさまざまな[出力ターゲット](/docs/output-targets)を簡単に生成できることです。

## ノードパッケージマネージャー（NPM）への公開

最初のステップで強く推奨されるステップは、[コンポーネントライブラリNPMを公開すること](](https://docs.npmjs.com/getting-started/publishing-npm-packages))です。 Npmは、ライブラリ、ツール、ユーティリティ、パッケージなどを共有するためのオンラインソフトウェアレジストリです。ライブラリがNPMに公開されると、他のプロジェクトはコンポーネントライブラリを依存関係として追加し、独自のプロジェクト内でコンポーネントを使用できます。


## `package.json`

`package.json`ファイルの目的は、パッケージのファイルを見つける方法について他のツールに指示を与え、パッケージに関する情報を提供することです。 たとえば、[Rollup](https://rollupjs.org/)や[Webpack](https://webpack.js.org/)などのバンドラーは、この構成を使用してプロジェクトのエントリファイルを検索します。

コンパイラーを使用する利点は、配布用にプロジェクトを最適にセットアップする方法に関するヘルプを提供できることです。 以下は、プロジェクトの `package.json`ファイル内にある一般的なセットアップです。

```json
{
  "main": "dist/index.js",
  "module": "dist/index.mjs",
  "es2015": "dist/esm/index.mjs",
  "es2017": "dist/esm/index.mjs",
  "types": "dist/types/interface.d.ts",
  "unpkg": "dist/ionic/ionic.js",
  "collection:main": "dist/collection/index.js",
  "collection": "dist/collection/collection-manifest.json",
  "files": [
    "dist/",
    "css/",
    "loader/"
  ]
}
```

| プロパティ | 説明                                                                                         | 推奨                       |
|----------|-----------------------------------------------------------------------------------------------------|-----------------------------------|
| `main`            | CommonJSモジュール形式のエントリファイル。 format.                                                  | `dist/index.js`                   |
| `module`          | モジュール形式のエントリファイル。 ESモジュールは、標準化され推奨される形式です。 | `dist/index.mjs`                  |
| `es2015`          | フレームワークのバンドルで一般的に使用されます。 bundling.                                                       | `dist/esm/index.mjs`              |
| `es2017`          | フレームワークのバンドルで一般的に使用されます。 bundling.                                                       | `dist/esm/index.mjs`              |
| `types`           | プロジェクトのタイプへのエントリファイル。                                                         | `dist/types/index.d.ts`           |
| `unpkg`           | プロジェクト[unpkg](https://unpkg.com/)CDNへのリクエストのエントリファイル。                   | `dist/{NAMESPACE}/{NAMESPACE}.js` |


`collection`プロパティは、他のSnteiclアプリケーションで遅延読み込みを可能にするために使用されます。

注： `dist`と`dist-custom-elements-bundle`の両方を配布する場合は、それらの1つをメインエントリとして選択するのが最善です。それはあなた次第です。