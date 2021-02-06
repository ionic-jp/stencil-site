---
title: Stencil Output Targets
description: Stencil Output Targets
url: /docs/output-targets
contributors:
  - adamdbradley
  - manucorporat
---

# 出力ターゲット

コンパイラのより強力な機能の1つは、コンポーネントが使用される _方法_ に応じてさまざまなビルドを生成する機能です。 Stencilは、アプリのソースを取得して、httpサーバーにデプロイするWebアプリ、[npm](https://www.npmjs.com/)で配布するサードパーティコンポーネントの遅延読み込みライブラリ、またはVanillaJSのカスタムエレメントなど、多数のターゲットにコンパイルできます。 デフォルトでは、Stecnilの出力ターゲットタイプは `www`であり、これはWebアプリに最適です。


## 出力ターゲットタイプ
 - [`dist-custom-elements-bundle`: カスタムエレメント](/docs/custom-elements)
 - [`dist`: ディストリビューション](/docs/distribution)
 - [`www`: Webサイト](/docs/www)
 - [`docs-readme`: マークダウンでフォーマットされたドキュメントのreadmeファイル](/docs/docs-readme)
 - [`docs-json`: JSONでフォーマットされたドキュメントデータ](/docs/docs-json)
 - [`docs-custom`: カスタムドキュメントの生成](/docs/docs-custom)

## 例:

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'dist'
    },
    {
      type: 'www'
    }
  ]
};
```


## ディファレンシャルバンドリング

「差分バンドリング」をサポートするために、コンパイラーが多数のバンドルを自動的に生成することに注意することも重要です。 これが意味するのは、本番ビルド中に、ステンシルは同じソースコードから最新のブラウザーとレガシーブラウザー（IE11）の両方のコードを生成するということです。 ディファレンシャルバンドルの利点は、最新のブラウザーがすべてのポリフィルと肥大化したレガシーJavaScriptを回避し、ブラウザーに既に組み込まれている最新のAPIを使用できることです。

以下の例では、2つのスクリプトタグがありますが、ユーザーが要求するのはそのうちの1つだけです。 IE11ユーザーの場合、`ES5`構文ですべてのポリフィルを含む`app.js`ファイルをダウンロードします。 最新のブラウザを使用しているユーザーは、[ESモジュール](https://developers.google.com/web/fundamentals/primers/modules)、[動的インポート](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import#Dynamic_Import)、[非同期/待機](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await)、[クラス](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)などの最新のJavaScript機能を使用する `app.esm.js`ファイルのみをダウンロードします。

注：IE11 ES5ファイルを生成するには、[buildEs5](https://stenciljs.com/docs/config#buildes5)をtrueに設定する必要があります

```markup
<script type="module" src="/build/app.esm.js"></script>
<script nomodule src="/build/app.js"></script>
```

## ドキュメントの自動生成

アプリがますます多くのコンポーネントで拡張され、チームのサイズとメンバーが時間の経過とともに調整され続けるにつれて、すべてのコンポーネントが適切に文書化され、文書自体が維持されることが重要です。 ドキュメントの保守は、開発者がしなければならない最も面白くないことのいくつかとともにすぐそこにありますが、それはそれを簡単にすることができないという意味ではありません。

ビルドプロセス全体を通じて、コンパイラは各コンポーネントからドキュメントを抽出して、JSDocsコメント、コンポーネントの各メンバーのタイプ（TypeScriptに感謝）、CSS変数（CSSカスタムプロパティ）を含めることができます。


### コンポーネントプロパティドキュメントの例：

`@Prop`に説明を追加するには、前の行にコメントを追加するだけです。

```tsx
/** (optional) 表示するアイコン */
@Prop() iconType = "";
```

### CSSの例

Stencilは、cssまたはscssファイル内のjsdocスタイルのコメントを介してCSS変数を指定すると、CSS変数も文書化します。

```css
 :root {
   /**
    * @prop --primary: プライマリヘッダーの色
    */
   --primary: blue;
 }
```
