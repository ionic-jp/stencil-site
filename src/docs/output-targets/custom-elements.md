---
title: Bundling Custom Elements with Stencil
description: Bundling Custom Elements with Stencil
url: /docs/custom-elements
contributors:
  - adamdbradley
---

# カスタムエレメントバンドル

`dist-custom-elements-bundle`出力ターゲットは、カスタムエレメントを単一のバンドルとして生成するために使用されます。 出力は最終的に「単一の」バンドルになりますが、コンポーネントがツリーシェイク可能であることを保証するために生成されます。 たとえば、コンポーネントライブラリに100個のコンポーネントがあり、外部プロジェクトがバンドルから1つのコンポーネントのみをインポートした場合、その1つのコンポーネントで使用されるコードのみがプロジェクトにプルされます。 これは、ステンシルがESモジュールを使用し、コンパイラーがバンドラーが解析して理解するためのわかりやすいコードを生成するためです。

```tsx
outputTargets: [
  {
    type: 'dist-custom-elements-bundle',
  },
];
```

## エクスポートされたカスタムエレメントの定義

デフォルトでは、カスタムエレメントバンドルは `dist/custom-elements/index.js`に書き込まれます。 このディレクトリは、出力ターゲットの `dir`設定を使用して設定できます。 生成された `index.js`ファイルには、各コンポーネントクラスへのエクスポートが含まれ、それらのスタイルがバンドルに含まれます。 ただし、このビルドでは、カスタムエレメントを自動的に定義したり、ポリフィルを適用したりすることはありません。

以下は、バンドル内のカスタムエレメントを定義する例です。クスポートされたカスタムエレメントの定義

```tsx
import { HelloWorld } from 'my-library/dist/custom-elements';

customElements.define('hello-world', HelloWorld);
```

コンポーネントが他のコンポーネントに依存している場合は、各コンポーネントを手動で登録する必要があります。 これは面倒な場合があるため、便宜上、バンドルは `defineCustomElements()`メソッドもエクスポートします。

`defineCustomElements()`が呼び出されると、バンドル内のすべてのコンポーネントが定義されます。 ただし、自動的には実行されず、インポートおよび実行されない場合は呼び出されません。 未使用のコンポーネントがインポートされている場合は、バンドルサイズが大きくなる可能性もあります。

```tsx
import { defineCustomElements } from 'my-library/dist/custom-elements';

defineCustomElements();
```

生成されたバンドルは各コンポーネントクラスをエクスポートし、すでにスタイルがバンドルされています。 ただし、カスタムエレメントを定義したり、ポリフィルを適用したりすることはありません。

## Making Assets Available

For performance reasons, the generated bundle does not include [local assets](/docs/local-assets) built within the JavaScript output, but instead it's recommended to keep static assets as external files. By keeping them external this ensures they can be requested on-demand, rather than either welding their content into the JS file, or adding many URLs for the bundler to add to the output. One method to ensure [local assets](/docs/local-assets) are available to external builds and http servers is to set the asset path using `setAssetPath()`.

The `setAssetPath()` function is used to manually set the base path where static assets can be found. For the lazy-loaded output target the asset path is automatically set and assets copied to the correct build directory. However, for custom elements builds, the `setAssetPath(path)` should be used to customize the asset path depending on where they are found on the http server.

If the component's script is a `type="module"`, it's recommended to use `import.meta.url`, such as `setAssetPath(import.meta.url)`. Other options include `setAssetPath(document.currentScript.src)`, or using a bundler's replace plugin to dynamically set the path at build time, such as `setAssetPath(process.env.ASSET_PATH)`.

## アセットを利用可能にする

パフォーマンス上の理由から、生成されたバンドルにはJavaScript出力内に構築された[ローカルアセット](/docs/local-assets)は含まれませんが、代わりに静的アセットを外部ファイルとして保持することをお勧めします。それらを外部に保持することにより、コンテンツをJSファイルに結合したり、バンドラーが出力に追加するための多くのURLを追加したりするのではなく、オンデマンドで要求できるようになります。 [ローカルアセット](/docs/local-assets)を外部ビルドとhttpサーバーで使用できるようにする1つの方法は、 `setAssetPath()`を使用してアセットパスを設定することです。

`setAssetPath()`関数は、静的アセットを見つけることができるベースパスを手動で設定するために使用されます。遅延ロードされた出力ターゲットの場合、アセットパスが自動的に設定され、アセットが正しいビルドディレクトリにコピーされます。ただし、カスタムエレメントビルドの場合は、 `setAssetPath(path)`を使用して、httpサーバー上の場所に応じてアセットパスをカスタマイズする必要があります。

コンポーネントのスクリプトが `type="module"`の場合は、 `setAssetPath(import.meta.url)`などの `import.meta.url`を使用することをお勧めします。他のオプションには、 `setAssetPath(document.currentScript.src)`、または `setAssetPath(process.env.ASSET_PATH)`などのバンドラーの置換プラグインを使用してビルド時にパスを動的に設定することが含まれます。


```tsx
import { defineCustomElements, setAssetPath } from 'my-library/dist/custom-elements';

setAssetPath(document.currentScript.src);
defineCustomElements();
```

アセットをアプリのパブリックディレクトリにコピーしてください。 この構成は、スクリプトがどのようにバンドルされているか、またはバンドルされていないか、およびアセットをどこからロードできるかによって異なります。 ファイルが本番ビルドディレクトリにコピーされる方法は、バンドラーまたはツールによって異なります。 以下の設定は、人気のあるバンドラーでこれを自動的に行う方法の例を示しています。

## カスタムエレメントの配布

コンポーネントライブラリは、[`@ionic/core`](https://www.npmjs.com/package/@ionic/core)と同様に、NPMで簡単に配布できます。 そこから、ライブラリの利用者は、ライブラリをプロジェクトにインポートする方法を決定できます。 `dist-custom-elements-bundle`の場合、デフォルトのインポート場所は`my-library/dist/custom-elements-bundle`ですが、これは `package.json`ファイル内でさらに構成できます。

カスタムエレメントにパッケージのエントリモジュールをバンドルさせるには、 `package.json`の`module`プロパティを次のように設定します。

また、パッケージの依存関係として必ず `@stencil/core`を設定してください。

```tsx
{
  "module": "dist/custom-elements-bundle/index.js",
  "dependencies": {
    "@stencil/core": "latest"
  },
  ...
}
```

注： `dist`と`dist-custom-elements-bundle`の両方を配布する場合は、 `module`エントリでどちらを使用できるかを選択するのはあなた次第です。

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

## これは「dist」出力ターゲットとどう違うのですか？

`dist-custom-elements-bundle`は、各コンポーネントを`HTMLElement`を拡張するスタンドアロンクラスとして構築します。 出力は標準化されたカスタムエレメントであり、スタイルはすでにアタッチされており、Stencilの遅延読み込みはありません。 これは、カスタムエレメント自体のバンドル、遅延読み込み、および定義をすでに処理しているプロジェクトに適している場合があります。

一方、 `dist`出力ターゲットは、バンドル構成をセットアップすることなく、コンポーネントが自身を遅延ロードできるようにしたいプロジェクト向けです。

幸い、両方のビルドを同じソースコードを使用して同時に生成し、同じディストリビューションで出荷することができます。 使用するビルドを決定するのは、コンポーネントライブラリの利用者次第です。

## ブラウザサポート

ライブラリをIE11で使用する場合は、必要なポリフィルのみをオンデマンドでロードするため、代わりに[`dist`出力ターゲット](/output-targets/dist)を使用することをお勧めします。 `dist-custom-elements-bundle`は、カスタムエレメント、Shadow DOM、およびCSS変数（基本的にIE11またはEdge 12-18ではない）をすでにサポートしている最新のブラウザーにのみ推奨されます。 このビルドをレガシーブラウザー内で使用する場合、これらのコンポーネントを使用するプロジェクトは、独自のポリフィルを提供し、出力をES5に正しくダウンレベルする必要があります。

良いニュースは、これらが現代のWeb開発ですでに広くサポートされていることです。

- [カスタムエレメントのサポート](https://caniuse.com/#feat=custom-elementsv1)
- [Shadow DOMのサポート](https://caniuse.com/#feat=shadowdomv1)
- [CSS変数のサポート](https://caniuse.com/#feat=css-variables)
- [ESモジュール](https://caniuse.com/#feat=es6-module)
- [async/await](https://caniuse.com/#feat=async-functions)
