---
title: Static Site Generation Basics in Stencil
description: Quick introducgion to configuring and using Static Site Generation in Stencil
url: /docs/static-site-generation-basics
contributors:
  - mlynch
  - adamdbradley
---

# 静的サイト生成の基本

(純粋にサーバーまたはクライアント時ではなく)ビルド時にコンポーネントをレンダリングすると、アプリのパフォーマンスが大幅に向上し、SEOへの影響が最大化されます。

Stencilで静的サイト生成を使用するには、ビルドコマンドを実行し、動的データをフェッチするコンポーネントライフサイクルメソッドからpromiseを返し、すべての既知のURLが適切に検出およびビルドされるようにする必要があります。

## 静的ビルド

Stencilは、デフォルトではコンポーネントを事前レンダリングしません。 ただし、ビルドは `--prerender`フラグを使用して事前レンダリングすることができます。

```bash
stencil build --prerender
```

## 動的データのレンダリング

多くのコンポーネントは、サーバーからフェッチされたデータに基づいてレンダリングする必要があります。 ステンシルは、コンポーネントが `componentWillLoad`のようなライフサイクルメソッドから`Promise`を返すことを許可することによってこれを処理します（これは `async/await`を使用することによっても達成できます）。

たとえば、これは、サーバーからデータをフェッチするまでStencilにコンポーネントのレンダリングを待機させる方法です。

```typescript
async componentWillLoad() {
  const ret = await fetch('https://.../api');

  this.thing = await ret.json();
}
```

## ルーターとの統合

Stencilは実際にコンポーネントに移動して実行するため、Stencilルーターを含むルーターを完全にサポートしています。

ルートパラメータと一致にアクセスするために必要な変更はありません。 ただし、事前にレンダリングされた静的コンテンツはそのパスで `index.html`ファイルをロードするものとして扱われるため、ルートが末尾のスラッシュを受け入れることができることを確認してください。これにより、ブラウザが末尾のスラッシュを追加する場合があります。

特に、Stencilルーターを使用している場合は、 `exact={true}`の使用法を再確認してください。これにより、末尾にスラッシュがロードされたときにルートが一致しなくなる可能性があります。

## ページとURLの検出

デフォルトでは、StencilはベースURLの `/`からアプリをクロールし、インデックスを作成する必要のあるすべてのパスを検出します。 デフォルトでは、これはビルド時にリンクされているページのみを検出しますが、アプリの可能なURLをビルドするように簡単に構成できます。

各ページが生成され、新しいリンクが見つかると、ステンシルは引き続きページをクロールして事前レンダリングします。

[prerender config](/docs/prerender-config)のドキュメントを参照して、これをさらにカスタマイズする方法を確認してください。


## 注意事項

事前レンダリング中に絶対に実行してはならないコードの領域がいくつかある場合があります。 特定のコードを回避するために、Stencilは事前レンダリングにスキップするように指示する条件付きの `Build.isBrowser`ビルドを提供します。 このユーティリティの使用方法の例を次に示します。

```tsx
import { Build } from '@stencil/core';

connectedCallback() {
  // Build.isBrowser is true when running in the
  // browser and false when being prerendered

  if (Build.isBrowser) {
    console.log('running in browser');
  } else {
    console.log('running in node while prerendering');
  }
}
```

また、ブラウザビルド用に生成された実際のランタイムには、 `if(Build.isBrowser)`ステートメントのために除外されたコードが含まれないことに注意してください。 上記の例では、 `console.log('running in browser')`のみがコンポーネントのランタイムに含まれます。
