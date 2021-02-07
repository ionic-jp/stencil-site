---
title: ローカルアセット
description: コンポーネントでのローカルアセットの使用
url: /docs/local-assets
contributors:
  - splitinfinities
  - simonhaenisch
---

# ローカルアセット

コンポーネントには通常、画像、ビデオ、またはあらゆる種類のデータファイルなどのアセットが必要です。Stencilには、このタスクを簡単にするための特定の機能が含まれています。

## コンポーネントのassetsDirs

ローカルアセットの使用方法の例を詳しく見てみましょう。

以下は、コンポーネント、スタイルシート、およびアセットディレクトリを含むコンポーネントのフォルダ構造の例です。

```bash
src/
  components/
    stencil-asset/
      assets/
        sunset.jpg
        beach.jpg
      stencil-asset.css
      stencil-asset.tsx
```

以下は、 `src`と呼ばれるプロパティに基づいてアセットを正しくロードする`stencil-asset.tsx`ファイルです。

```tsx
import { Component, Prop, getAssetPath, h } from '@stencil/core';

@Component({
  tag: 'stencil-asset',
  styleUrl: 'stencil-asset.css',
  assetsDirs: ['assets']
})
export class StencilAsset {

  @Prop() image = "sunset.jpg";

  render() {
   return <img src={getAssetPath(`./assets/${this.image}`)} />
  }
}
```

この例からわかるように、コンポーネントデコレータには `./assets`フォルダを指す`assetsDirs`プロパティがあり、これはStencilコンパイラにそのフォルダを配布フォルダ（ `dist`または`www`）にコピーするように指示します。

次に、コンポーネントのロジック内で、 `getAssetPath`関数（`@stencil/core`からインポート）を使用して、アセットのロード元となる絶対パスを決定します。

### ユースケース

私たちが思いついたいくつかのユースケースは次のとおりです。

- SVGのコレクションからの読み込み
- オンデマンドでフォントを読み込んで適用する
- 画像またはビデオの読み込み