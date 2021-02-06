---
title: Hydrate App
description: Hydrate App
url: /docs/hydrate-app
contributors:
  - adamdbradley
  - dgautsch
  - bitflower
---

# ハイドレートアプリ

ハイドレートアプリは同じコンポーネントのバンドルですが、NodeJSサーバーでハイドレートしてHTMLを生成できるようにコンパイルされています。 これは、事前レンダリングが内部で使用するのと同じNodeJSアプリであり、Angular UniversalSSRがIonicに使用するものと同じです。 ステンシルコンポーネントと同様に、ハイドレートアプリ自体は1つのフレームワークに制限されていません。

_StencilはSSRまたは事前レンダリングにPuppeteerを**使用しない**ことに注意してください。_

## ハイドレートアプリの使用方法

サーバー側レンダリング（SSR）は、事前レンダリングと同様の方法で実行できます。 `--prerender` CLIフラグを使用する代わりに、` {type： 'dist-hydrate-script'} `を` stencil.config.js`に追加できます。 これにより、ルートプロジェクトディレクトリに「ハイドレート」アプリが生成され、ノードサーバーでインポートして使用できるようになります。

コンポーネントライブラリを公開した後、次のようにハイドレートアプリをサーバーのコードにインポートできます。

```javascript
import { hydrateDocument } from 'yourpackage/hydrate';
```

このアプリには、 `hydrateDocument`と`renderToString`の2つの関数が付属しています。 `hydrateDocument`の利点は、すでに解析されている既存の[ドキュメント](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDocument)を取得できることです。 代わりに、 `renderToString`関数は生のhtml文字列を入力し、ドキュメントの解析を行います。

**hydrateDocument**：Webページを提供する前に、サーバーの応答ロジックの一部として `hydrateDocument`を使用できます。 `hydrateDocument`は、[document](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDocument)とconfigオブジェクトの2つの引数を取ります。 この関数は、ハイドレイトされた結果でpromiseを返します。 結果のhtmlは、返された結果の `html`プロパティから解析できます。

*IonicAngularサーバーからの例*

 ```javascript
import { hydrateDocument } from 'yourpackage/hydrate';

export function hydrateComponents(doc) {
  return hydrateDocument(doc)
    .then((hydrateResults) => {
      // execute logic based on results
      console.log(hydrateResults.html);
      return hydrateResults;
    });
}
```

#### hydrateDocumentオプション
  - `canonicalUrl` - string
  - `constrainTimeouts` - bool
  - `clientHydrateAnnotations` - bool
  - `cookie` - string
  - `direction ` - string
  - `language ` - string
  - `maxHydrateCount` - number
  - `referrer` - string
  - `removeScripts` - bool
  - `removeUnusedStyles` - bool
  - `resourcesUrl` - string
  - `timeout` - number
  - `title` - string
  - `url` - string
  - `userAgent` - string

**renderToString**：ハイドレートアプリには、 `HydrateResults`の約束を返すhtml文字列を渡すことができる`renderToString`関数もあります。 2番目のパラメーターは、マークアップの出力を変更できる構成オブジェクトです。 `hydrateDocument`と同様に、結果の文字列は`html`プロパティから解析できます。


*IonicCoreからの例*

```javascript
const results = await hydrate.renderToString(srcHtml, {
  prettyHtml: true,
  removeScripts: true
});

console.log(results.html);
```

#### renderToString Options

  - `approximateLineWidth` - number
  - `prettyHtml` - boolean
  - `removeAttributeQuotes` - boolean
  - `removeBooleanAttributeQuotes` - boolean
  - `removeEmptyAttributes` - boolean
  - `removeHtmlComments` - boolean
  - `afterHydrate` - boolean
  - `beforeHydrate` - boolean
