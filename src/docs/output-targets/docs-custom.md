---
title: Custom Docs Generation
description: Custom Docs Generation
url: /docs/docs-custom
contributors:
  - adamdbradley
  - manucorporat
  - arayik-yervandyan
---

# カスタムドキュメントの生成

Stencilは、ユーザーが生成されたdocs-jsonデータにアクセスできる `docs-custom`というタイトルの出力ターゲットを公開します。 この機能を使用して、カスタムマークダウンを生成したり、ビルド中にjsonデータに対して他のロジックを実行したりできます。 他のドキュメント出力ターゲットと同様に、 `strict`モードがサポートされています。

この出力ターゲットを利用するには、ステンシル構成に以下を追加するだけです。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'docs-custom',
      generator: (docs: JsonDocs) => {
          // Custom logic goes here
      }
    }
  ]
};
```

## Config

| プロパティ    | 説明                                                                              | デフォルト値 |
|-------------|------------------------------------------------------------------------------------------|---------|
| `generator` | docs-jsonデータを引数として持つ関数。                                          |         |
| `strict`    | trueに設定すると、ドキュメントが不足している場合は常にステンシルが警告を出力します。   | `false` |



# カスタムドキュメントデータモデル

生成されたドキュメントJSONデータは、Stencilコアが検出したコンポーネントと`timestamp`や「compiler」などのメタ情報で構成されるメインの`components`配列で構成される`JsonDocs`タイプになります。

## JsonDocs

| プロパティ    | 説明                                                                              |
|-------------|------------------------------------------------------------------------------------------|
| `components` | コンポーネント情報で構成される `JsonDocsComponent[]`のタイプの配列|
| `timestamp`    | タイムスタンプ付きの `string`   |
| `compiler`    | `typescriptVersion`、`compiler`、`version`を持つ`Object`   |

## JsonDocsComponent

| プロパティ    | 説明                                                                              |
|-------------|------------------------------------------------------------------------------------------|
| `dirPath` コンポーネントディレクトリパス |
| `fileName`    | ファイル名 |
| `filePath`    | ファイルパス |
| `readmePath`    | Readmeファイルのパス|
| `usagesDir`    | 使用ディレクトリのパス |
| `encapsulation`    | `encapsulation`タイプコンポーネント。可能な値は`shadow`, `scoped`, `none`  |
| `tag`    | `.tsx`ファイルに機銃されているコンポーネントタグ  |
| `readme`    | コンポーネントreadmeファイルの最初の行の内容 |
| `docs`    | `@Component`の上に記述された説明。例: /** ドキュメントの例 **/ |
| `docsTags`    |  `.tsx`ファイルに書かれた注釈（JSDocの方法で）はここに集められます  |
| `usage`    |    |
| `props`    | コンポーネントプロパティ情報の配列 |
| `methods`    | コンポーネントメソッド情報の配列 |
| `events`    |    |
| `listeners`    |    |
| `styles`    |    |
| `slots`    |    |
| `parts`    |    |
| `dependents`    |  現在のコンポーネントが使用されているコンポーネントの配列  |
| `dependencies`    | 現在のコンポーネントで使用されているコンポーネントの配列  |
| `dependencyGraph`    | 結合するコンポーネントのツリーについて説明します |

