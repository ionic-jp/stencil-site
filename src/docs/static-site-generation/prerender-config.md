---
title: プリレンダリングのコンフィグ
description: プリレンダリングのコンフィグ
url: /docs/prerender-config
contributors:
  - adamdbradley
  - ryan3E0
  - dgautsch
---

# 静的サイト生成（SSG）の事前レンダリング構成

`1.13.0`以降、オプションのprerender設定は、`www`出力ターゲットを事前レンダリングするときに使用できます。 `prerender.config.ts`ファイルは、HTMLにシリアル化される前に、各ページの解析されたドキュメントをさらに更新できます。

`stencil.config.ts`内で、`prerenderConfig`を使用して事前レンダリング設定ファイルパスへのパスを設定します
次のようなプロパティ:

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'www',
      baseUrl: 'https://stenciljs.com/',
      prerenderConfig: './prerender.config.ts',
    }
  ]
};
```

次に、 `prerender.config.ts`ファイル内で、`PrerenderConfig`インターフェースを使用して `config`オブジェクトをエクスポートする必要があります。

```tsx
import { PrerenderConfig } from '@stencil/core';
export const config: PrerenderConfig = {
  ...
};
```

| コンフィグ | 説明 | デフォルト値 |
|--------|-------------|---------|
| `afterHydrate(document, url)` | 各 `document`がハイドレイトされた後、HTML文字列にシリアル化される前に実行します。 フックには `document`とその`URL`が渡されます。 |  |
| `beforeHydrate(document, url)` | 各 `ドキュメント`がハイドレイトされる前に実行します。 フックには「ドキュメント」が渡され、「URL」になります。 |  |
| `afterSerializeTemplate(html)` | テンプレートDocumentオブジェクトがHTML形式の文字列にシリアル化された後に実行されます。 事前にレンダリングされたすべてのページのベーステンプレートとして使用されるHTML文字列を返します。 |  |
| `beforeSerializeTemplate(document)` | テンプレートDocumentオブジェクトがHTML形式の文字列にシリアル化される前に実行されます。 シリアル化されるドキュメントを返します。これは、事前にレンダリングされたすべてのページのベーステンプレートhtmlになります。 |  |
| `canonicalUrl(url)` | 事前にレンダリングされたすべてのページの `<head>`に配置される正規の `<link>`タグを生成するために使用されるフック。 `null`を返しても、正規のURLタグはページに追加されません。 |  |
| `crawlUrls` | 事前レンダリング中に、 `<ahref>`要素内で見つかった同一生成元のURLをクロールします。 | `true` |
| `entryUrls` | 事前レンダリングを開始するURL。 デフォルトでは、 `/`のルートURLが使用されます。 | `['/']` |
| `filterAnchor(attrs, base)` | 指定された `<a>`要素をクロールするかどうかにかかわらず `true`を返します。 |  |
| `filterUrl(url, base)` | 指定されたURLを事前にレンダリングする必要があるかどうかにかかわらず、「true」を返します。 |  |
| `filePath(url, filePath)` | 事前にレンダリングされたHTMLコンテンツが書き込まれるファイルパスを返します。 |  |
| `hydrateOptions(url)` | 個々の事前レンダリングされたページに使用するハイドレートオプションを返します。 |  |
| `loadTemplate(filePath)` | テンプレートファイルの内容を返します。 テンプレートは、事前にレンダリングされたすべてのページに使用されるベースHTMLです。 |  |
| `normalizeUrl(href, base)` | 指定された文字列と現在のページのベースURLからページのURLを正規化するために使用されます。 アンカーの `href`属性値を読み取り、それを`URL`に正規化するときに主に使用されます。  |  |
| `staticSite` | 静的サイト生成（SSG）。 StencilのクライアントサイドJavaScript、カスタム要素、またはプリロードモジュールは含まれません。 | `false` |
| `trailingSlash` | 事前にレンダリングされたURLの末尾に「/」が必要かどうか? | `false` |


## 個々のページのハイドレートオプション

`prerender.config.ts`を使用した事前レンダリングプロセス全体の設定に加えて、次のこともできます。
各ページごとに個別のハイドレートオプションを設定します。 `hydrateOptions(url)`フックを使用して、各ページをさらに構成できます。 以下は、各ページのオプションを返す `hydrateOptions()`フックを使用した事前レンダリング構成の例です。

```tsx
import { PrerenderConfig } from '@stencil/core';

export const config: PrerenderConfig = {
  hydrateOptions(url) {
    return {
      prettyHtml: true
    };
  }
};


```
| オプション | 説明 | デフォルト値 |
|--------|-------------|---------|
| `addModulePreloads` | 最終的に要求されるモジュールに `<link rel =" modulepreload ">`を追加します。 | `true` |
| `approximateLineWidth` | HTMLがその範囲内に留まろうとするおおよその線幅を設定します。 HTMLは正確な行幅で分割できないことが多いため、これは「概算」であることに注意してください。さらに、作成された新しい行は、属性の前や単語間のスペースなど、HTMLにすでにwhitespceが含まれている場所です。 | `100` |
| `canonicalUrl` | `<head>`内の `<link rel ="canonical">`タグに `href`属性を設定します。値が定義されていない場合は、正規リンクタグが `<head>`に含まれていないことを確認します。 |  |
| `clientHydrateAnnotations` | クライアント側のJavaScriptがHTMLの構造を読み取り、各コンポーネントを再構築するために使用するHTMLコメントと属性を含めます。 | `true` |
| `constrainTimeouts` | `setTimeout()`を1msに制限しますが、それでも非同期です。また、 `setInterval()`の起動は1回のみで、1ミリ秒に制限されています。 | `true` |
| `cookie` | `document.cookie`を設定します |  |
| `direction` | トップレベルの `<html>`に `dir`属性を設定します。 |  |
| `excludeComponents` | ここにリストされているコンポーネントタグ名は、事前にレンダリングされたり、クライアント側でハイドレートされたりすることはありません。ここにリストされているコンポーネントはカスタム要素として無視され、 `<div>`と同じように扱われます。 |  |
| `inlineExternalStyleSheets` | 代わりに、 `<link rel ="stylesheet">`の外部スタイルシートが `<style>`要素にインライン化されます。 | `true` |
| `language` | トップレベルの `<html>`に `lang`属性を設定します。 |  |
| `maxHydrateCount` | 1ページでハイドレートするコンポーネントの最大数。 | `300` |
| `minifyScriptElements` | `<script>`要素内のJavaScriptコンテンツを縮小します。 | `true` |
| `minifyStyleElements` | `<style>`要素内のCSSコンテンツを縮小します。 | `true` |
| `prettyHtml` | HTMLを適切にインデントされた形式でフォーマットします。 | `false` |
| `referrer` | `document.referrer`を設定します。 |  |
| `removeAttributeQuotes` | 可能な場合は、属性値から引用符を削除してください。 | `true` |
| `removeBooleanAttributeQuotes` | `hidden`や`checked`などの標準化された `boolean`属性から`=""`を削除します。 | `true` |
| `removeEmptyAttributes` | 値が空の場合は、次の標準化された属性を削除します： `class`、`dir`、 `id`、`lang`、および `name`、`title`。 | `true` |
| `removeHtmlComments` | HTMLコメントを削除します。 | `true` |
| `removeScripts` | `document`で見つかったすべての`<script>`要素を削除します。 | `false` |
| `removeUnusedStyles` | `document`内の要素で使用されていないCSSを削除します。 | `true` |
| `resourcesUrl` | ランタイムがアセットディレクトリなどのリソースに使用するURL。 |  |
| `runtimeLogging` | ランタイムコンソールログをNodeJSプロセスに出力します。 | `false` |
| `staticComponents` | ここにリストされているコンポーネントタグは、事前レンダリングまたはサーバーサイドレンダリングのみであり、クライアントサイドでハイドレイトされません。これは、動的ではなく、ブラウザ内でカスタム要素として定義する必要がないコンポーネントに役立ちます。たとえば、ヘッダーまたはフッターコンポーネントは、クライアント側のJavaScriptを必要としない良い例です。 |  |
| `staticDocument` | `document`全体は静的でなければなりません。これは、サイト全体ではなく、静的である必要がある特定のページに役立ちます。サイト全体を静的にする必要がある場合は、代わりにprerender構成の `staticSite`プロパティを使用してください。特定のコンポーネントのみを静的にする必要がある場合は、代わりに `staticComponents`を使用してください。 | `false` |
| `timeout` | タイムアウトエラーがスローされるまで、ページのレンダリングが終了するまで待機するミリ秒数。 | `15000` |
| `title` | `document.title`を設定します |  |
| `url` | `location.href`を設定します |  |
| `userAgent` | `navigator.userAgent`を設定します |  |
