---
title: ドキュメントJSONデータ出力ターゲット
description: ドキュメントJSONデータ出力ターゲット
url: /docs/docs-json
contributors:
  - adamdbradley
  - snaptopixel
  - manucorporat
  - amwmedia
  - mrtnmgs
  - marcjulian
  - seanwuapps
---

# ドキュメントJsonデータ

マークダウンでフォーマットされた自動生成されたreadmeファイルは便利ですが、すべてのドキュメントをjsonデータの形式で取得したほうがよいシナリオもあります。 ドキュメントをjsonとしてビルドするには、 `--docs-json`フラグを使用し、その後にjsonファイルを書き込む場所のパスを続けます。

```tsx
  scripts: {
    "docs.data": "stencil build --docs-json path/to/docs.json"
  }
```

別のオプションは、ビルドごとにこのファイルを自動生成するために、 `docs-json`出力ターゲットを`stencil.config.ts`に追加することです。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'docs-json',
      file: 'path/to/docs.json'
    }
  ]
};
```

JSON出力のtypescript宣言を確認してください：https://github.com/ionic-team/stencil/blob/master/src/declarations/stencil-public-docs.ts

## CSS変数

Stencilは、css/scssファイル内のjsdocスタイルのコメントを介してCSS変数を指定すると、CSS変数も文書化します。

```css
/**
 * @prop --background: ボタンの背景
 * @prop --background-activated: アクティブ化されたときのボタンの背景
 * @prop --background-focused: フォーカスされたときのボタンの背景
 */
```

## スロット

スロットは、コンポーネントデコレータの上のドキュメントコメントに `@slot`タグを追加することで文書化できます。

```tsx
/**
 * @slot slotの名前 - slotの説明
 * @slot buttonContent - ボタンのコンテンツ用のスロット
 */
```


## 使用法

コンポーネントの `usage`サブディレクトリにある`.md`ファイルのコンテンツは、生成されたjsonの `usage`プロパティに追加されます。

```bash
src/
  components/
    my-component/
      usage/
        usage-example.md
        another-example.md
      my-component.css
      my-component.tsx
```


## カスタムJSDocsタグ

事前定義されたJSDocタグを読み取ることに加えて、ユーザーは独自のカスタムタグを提供できます。これもJSONデータに含まれます。 これにより、チームが独自のドキュメントと規則を提供して、JSONデータ内に構築することが容易になります。 たとえば、次のようにソースコードにコメントを追加した場合：

```tsx
/**
 * @myDocTag someName - some value
 * @myOtherDocTag someOtherName - some other name
 */
```

最終的には次のようなJSONデータになります。

```tsx
"docsTags": [
  {
    "text": "someName - some value",
    "name": "myDocTag"
  },
  {
    "text": "someOtherName - some other name",
    "name": "myOtherDocTag"
  }
],
```
