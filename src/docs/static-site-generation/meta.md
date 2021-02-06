---
title: SEO Meta Tags in SSG
description: Managing meta tags for SEO and social media embeding in Stencil Static Sites
url: /docs/static-site-generation-meta-tags
contributors:
  - mlynch
  - adamdbradley
---

# SEOメタタグと静的サイトの生成

Webアプリは、SEOを最大化し、優れたソーシャルメディア埋め込みエクスペリエンスを提供するために、コンテンツに関する詳細なメタ情報を一覧表示する必要があります。

Stencilの事前レンダリングの利点の1つは、ほとんどのDOMAPIがNodeJS環境でも利用できることです。たとえば、ドキュメントのタイトルを設定するには、 `document.title ="PageTitle"`を実行するだけです。または、「document.head」や「document.createElement（ 'meta'）」など、ブラウザにある通常のDOMAPIを使用する場合と同じようにメタタグを追加および更新できます。これは、コンポーネントランタイムが、事前レンダリング全体でカスタム作業の多くをすでに実行できる可能性があることを意味します。

とはいえ、Prerender Configには、個々のページを変更できる多くのオプションも付属しています。たとえば、 `afterHydrate(document.url)`フックを使用して、HTML文字列にシリアル化される前に、解析された `document`を更新できます。 `document`引数はウェブページにある`document`と同じように使用でき、 `url`引数はレンダリングされるページの`URL`の場所です。

以下の例では、 `afterHydrate(document.url)`フックがurlのパス名からドキュメントタイトルを設定しています。

```tsx
import { PrerenderConfig } from '@stencil/core';

export const config: PrerenderConfig = {
  afterHydrate(document, url) {
    document.title = url.pathname;
  }
};
```

## @stencil/helmet

Stencilを使用して静的サイトを構築する場合、 `@stencil/helmet`パッケージを使用すると、メタタグを動的に管理するのが簡単になります。

開始するには、パッケージをインストールします。

```bash
npm install --save-dev @stencil/helmet
```

次に、ドキュメントの `<head>`のメタタグを更新するには、 `<Helmet>`関数コンポーネントを使用します。

```typescript
import Helmet from '@stencil/helmet';

const MyComponent = ({ title, description }: Props) => (
  <Helmet>
    <title>{title}</title>
    <meta name="description" content={description} />
    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:site" content="..." />
    <meta name="twitter:creator" content="..." />
    <meta name="twitter:title" content={title} />
    <meta name="twitter:description" content={description} />
    <meta name="twitter:image" content="..." />

    <meta property="fb:page_id" content="..." />
    <meta property="og:url" content="..." />
    <meta property="og:type" content="..." />
    <meta property="og:title" content="..." />

    <meta property="og:image" content="..." />
    <meta property="og:description" content="..." />
    <meta property="og:site_name" content="..." />
    <meta property="article:publisher" content="..." />
    <meta property="og:locale" content="..." />
  </Helmet>
)
```

表示されているすべてのコンポーネントでこれを使用すると、 `<head>`のメタタグが更新されます。
