---
title: Stencilでつくるカスタムエレメント
description: Stencilでつくるカスタムエレメント
url: /docs/custom-elements
contributors:
  - adamdbradley
  - rwaskiewicz
  - splitinfinities
---

# カスタムエレメント

`dist-custom-elements` 出力ターゲットは、ツリーシェイクに最適化された方法でカスタムエレメントを生成するために使用され、フロントエンドフレームワークとの統合を使用する際に推奨される方法です。例えば、コンポーネントライブラリに100個のコンポーネントがあり、外部プロジェクトがそのディレクトリから1つのコンポーネントだけをインポートした場合、その1つのコンポーネントで使用されているコードだけがプロジェクトに取り込まれることになります。これは、StencilがESモジュールを使用していることと、コンパイラがバンドルラーが解析して理解できるようなフレンドリーなコードを生成しているためです。

```tsx
outputTargets: [
  {
    type: 'dist-custom-elements',
  },
];
```

## エクスポートされたカスタムエレメントの定義

デフォルトでは、カスタムエレメントのファイルは `dist/components/index.js` に書き込まれます。このディレクトリは、出力ターゲットの `dir` コンフィグで設定できます。生成された `index.js` ファイルには、各コンポーネントクラスのエクスポートが含まれており、それらのスタイルがバンドル内に含まれます。ただし、このビルドでは、自動的にカスタム要素を定義したり、ポリフィルを適用したりすることはありません。

以下に、カスタム要素の定義の例を示します。

```tsx
import { HelloWorld } from 'my-library/dist/components/hello-world';

customElements.define('hello-world', HelloWorld);
```

生成されたファイルは、各コンポーネントクラスをエクスポートし、すでにスタイルがバンドルされています。しかし、カスタム要素の定義やポリフィルの適用は行われません。また、インポートされたコンポーネントの依存関係があれば、それも読み込む必要があります。

## Config

### autoDefineCustomElements

By default, consumers of the `custom-elements` output target need to either register each Stencil component in the
bundle manually, or call a convenience method, `defineCustomElements()`, that is exported as a part of the bundle to
define every component in the bundle. This behavior can be cumbersome, especially when only a handful of components are
needed and/or those components have several child components (and their children have children, etc.).

Setting this flag to `true` will recursively define all children components for a Stencil component when it is
registered. Users of this flag should note that this may increase their bundle size by automatically defining &
registering child components.

This flag defaults to `false` when omitted from a Stencil configuration file.

> Note: At this time, components created not using JSX may not be automatically defined. This is a known limitation of the API and users should be aware of it

## Making Assets Available

パフォーマンス上の理由から、生成されたバンドルには、JavaScriptの出力に組み込まれた[local assets](/docs/local-assets)は含まれていません。外部ファイルにしておくことで、その内容をJSファイルに溶接したり、バンドルラーが出力に追加するために多くのURLを追加するのではなく、必要に応じてリクエストできるようになります。ローカルアセット](/docs/local-assets)を外部のビルドやHTTPサーバーから利用できるようにする方法の一つとして、`setAssetPath()`を使ってアセットパスを設定する方法があります。

`setAssetPath()`関数は、スタティックアセットが存在するベースパスを手動で設定するために使用します。遅延ロードされた出力ターゲットの場合、アセットパスは自動的に設定され、アセットは正しいビルドディレクトリにコピーされます。しかし、カスタムエレメントのビルドでは、アセットパスをhttpサーバ上のどこで見つかるかに応じてカスタマイズするために、`setAssetPath(path)`を使用する必要があります。

コンポーネントのスクリプトが `type="module"` の場合は、 `setAssetPath(import.meta.url)` のように `import.meta.url` を使うことが推奨されます。その他のオプションとしては、`setAssetPath(document.currentScript.src)`や、`setAssetPath(process.env.asset_PATH)`のように、バンドルラーのreplaceプラグインを使用して、ビルド時にパスを動的に設定することもできます。

## アセットを利用可能にする

パフォーマンス上の理由から、生成されたバンドルにはJavaScript出力内に構築された[ローカルアセット](/docs/local-assets)は含まれませんが、代わりに静的アセットを外部ファイルとして保持することをお勧めします。それらを外部に保持することにより、コンテンツをJSファイルに結合したり、バンドラーが出力に追加するための多くのURLを追加したりするのではなく、オンデマンドで要求できるようになります。 [ローカルアセット](/docs/local-assets)を外部ビルドとhttpサーバーで使用できるようにする1つの方法は、 `setAssetPath()`を使用してアセットパスを設定することです。

`setAssetPath()`関数は、静的アセットを見つけることができるベースパスを手動で設定するために使用されます。遅延ロードされた出力ターゲットの場合、アセットパスが自動的に設定され、アセットが正しいビルドディレクトリにコピーされます。ただし、カスタムエレメントビルドの場合は、 `setAssetPath(path)`を使用して、httpサーバー上の場所に応じてアセットパスをカスタマイズする必要があります。

コンポーネントのスクリプトが `type="module"`の場合は、 `setAssetPath(import.meta.url)`などの `import.meta.url`を使用することをお勧めします。他のオプションには、 `setAssetPath(document.currentScript.src)`、または `setAssetPath(process.env.ASSET_PATH)`などのバンドラーの置換プラグインを使用してビルド時にパスを動的に設定することが含まれます。


```tsx
import { setAssetPath } from 'my-library/dist/components';

setAssetPath(document.currentScript.src);
```

アセットは必ずアプリ内の公開ディレクトリにコピーしてください。この設定は、スクリプトがどのようにバンドルされているか、あるいはバンドルされていないか
また、アセットをどこから読み込むかによっても異なります。本番のビルドディレクトリにどのようにファイルをコピーするかは、バンドルラーやツールに依存します。
以下の設定は、一般的なバンドルラーで自動的に行う方法の例です。

## カスタムエレメントの配布

=======
あなたのコンポーネントライブラリは、[`@ionic/core`](https://www.npmjs.com/package/@ionic/core)のやり方と同様に、NPMで簡単に配布することができます。そこから、あなたのライブラリの消費者は、あなたのライブラリを自分のプロジェクトにどのようにインポートするかを決めることができます。dist-custom-elements`では、デフォルトのインポート先は `my-library/dist/components` ですが、これは `package.json` ファイル内でさらに設定することができます。

カスタムエレメントのインデックスをパッケージのエントリーモジュールにするには、`package.json` の `module` プロパティを次のように設定します。

また、パッケージの依存関係として必ず `@stencil/core`を設定してください。

```tsx
{
  "module": "dist/components/index.js",
  "dependencies": {
    "@stencil/core": "latest"
  },
  ...
}
```

注意: `dist` と `dist-custom-elements` の両方を配布している場合は、どちらを `module` のエントリで利用できるようにするかは、あなた次第です。

これで、ライブラリを[Node Package Manager(NPM)](https://www.npmjs.com/)に公開できます。 `package.json`ファイルの設定と公開の詳細については、[コンポーネントライブラリをNPMに公開する](/docs/Publishing)を参照してください。

## Bundler構成の例

カスタムエレメントバンドルを使用する手順は、使用しているバンドラーによって異なります。 これらの例は、ユーザーがwebpackとRollupを使用してコンポーネントを使用するのに役立ちます。

次の例では、コンポーネントライブラリが `my-library`としてNPMに公開されていることを前提としています。 これを、実際にライブラリを公開するときに使用する名前に変更する必要があります。

ユーザーは、ライブラリをインポートする前にライブラリをインストールする必要があります。

```bash
npm install my-library
```

### webpack.config.js

webpackの設定は次のようになります。 アセットがライブラリの `node_module`フォルダから`CopyPlugin`ユーティリティを介して `dist/assets`にコピーされる方法に注意してください。 ライブラリに[ローカルアセット](/docs/local-assets)が含まれている場合、これは重要です。

```javascript
const path = require('path');
const CopyPlugin = require('copy-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
  plugins: [
    new CopyPlugin({
      patterns: [
        {
          from: path.resolve(__dirname, 'node_modules/my-library/dist/my-library/assets'),
          to: path.resolve(__dirname, 'dist/assets'),
        },
      ],
    }),
  ],
};
```

### rollup.config.js

ロールアップ構成は、次のようになります。 アセットがライブラリの `node_module`フォルダから`rollup-copy-plugin`ユーティリティを介して `dist/assets`にコピーされる方法に注意してください。 ライブラリに[ローカルアセット](/docs/local-assets)が含まれている場合、これは重要です。

```javascript
import path from 'path';
import commonjs from '@rollup/plugin-commonjs';
import copy from 'rollup-plugin-copy';
import postcss from 'rollup-plugin-postcss';
import resolve from '@rollup/plugin-node-resolve';

export default {
  input: 'src/index.js',
  output: [{ dir: path.resolve('dist/'), format: 'es' }],
  plugins: [
    resolve(),
    commonjs(),
    postcss({
      extensions: ['.css'],
    }),
    copy({
      targets: [
        {
          src: path.resolve(__dirname, 'node_modules/my-library/dist/my-library/assets'),
          dest: path.resolve(__dirname, 'dist'),
        },
      ],
    }),
  ],
};
```

## "dist "や "dist-custom-element-bundle "の出力ターゲットとはどう違うのですか?

`dist-custom-elements` は、各コンポーネントを `HTMLElement` を拡張したスタンドアローンのクラスとしてビルドします。出力されるのは、スタイルがすでにアタッチされ、Stencilのレイジーローディングが一切ない、標準化されたカスタムエレメントです。これは、バンドル、レイジーローディング、カスタムエレメントの定義をすでに自分たちで行っているプロジェクトにとっては好ましいものでしょう。

`dist-custom-elements-bundle` 出力ターゲットは `dist-custom-elements` とほぼ同じですが、[Stencil v2.12.0](https://github.com/ionic-team/stencil/releases/tag/v2.12.0) で非推奨となり、ツリーシェイク機能が向上した `dist-custom-elements` が使用されるようになりました。Stencil の React、Vue、Angular の出力先では、この理由により `dist-custom-elements` を使用しています。

一方、 `dist`出力ターゲットは、バンドル構成をセットアップすることなく、コンポーネントが自身を遅延ロードできるようにしたいプロジェクト向けです。

幸いなことに、すべてのビルドは、同じソースコードを使って同時に生成され、同じディストリビューションで出荷することができます。どのビルドを使用するかは、コンポーネントライブラリの消費者が決めることになります。

## ブラウザサポート

このライブラリを IE11 で使用する場合は、代わりに [`dist` 出力対象](/docs/distribution) を使用することをお勧めします。必要なポリフィルだけをオンデマンドで読み込むことができます。 `dist-custom-elements`は、Custom Elements、Shadow DOM、CSS Variablesをすでにサポートしているモダンブラウザにのみ推奨されます（基本的にIE11やEdge 12-18は対象外）。このビルドをレガシーブラウザで使用する場合は、これらのコンポーネントを消費するプロジェクトが独自のポリフィルを提供し、出力を正しくES5にダウンレベルする必要があります。

良いニュースは、これらが現代のWeb開発ですでに広くサポートされていることです。

- [カスタムエレメントのサポート](https://caniuse.com/#feat=custom-elementsv1)
- [Shadow DOMのサポート](https://caniuse.com/#feat=shadowdomv1)
- [CSS変数のサポート](https://caniuse.com/#feat=css-variables)
- [ESモジュール](https://caniuse.com/#feat=es6-module)
- [async/await](https://caniuse.com/#feat=async-functions)
