---
title: ドキュメントのReadme自動生成
description: ドキュメントのReadme自動生成
url: /docs/docs-readme
contributors:
  - adamdbradley
  - snaptopixel
  - manucorporat
  - amwmedia
  - mrtnmgs
  - marcjulian
---

# ドキュメントのReadmeマークダウンファイルの自動生成

Stencilは、マークダウンで `readme.md`ファイルを自動生成することができます。 これはオプトイン機能であり、readmeファイルを兄弟として同じディレクトリ内のコンポーネントに保存します。 この機能を使用すると、他の人が1つのコンポーネントに関するフォーマットされたドキュメントを簡単に見つけて読むことができます。 特に、 `readme.md`ファイルがGithubのディレクトリ内に配置されると、デフォルトでreadmeマークダウンファイルがページのプライマリコンテンツになります。

readmeファイルを自動生成するには、 `docs-readme`出力ターゲットを`stencil.config.ts`に追加します。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    { type: 'docs-readme' }
  ]
};
```

別のオプションは、次のようなフラグ `--docs-readme`を追加することです。

```bash
stencil build --docs-readme
```

## 自動生成されたファイルへのカスタムマークダウンの追加

`readme.md`ファイルを生成したら、独自のマークダウンコンテンツでカスタマイズできます。 `<!--以下に自動生成-->`というコメントの上に独自のマークダウンを追加するだけです。

## カスタムフッター

フッターの削除またはカスタマイズは、出力ターゲットに `footer`プロパティを追加することで実行できます。 必要に応じて、マークダウンを使用してフッターを拡張できます。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'docs-readme',
      footer: '*Built with love!*',
    }
  ]
};
```

## ディレクトリへの生成

デフォルトでは、readmeファイルは対応するコンポーネントディレクトリに生成されます。 この動作は、出力ターゲット構成の `dir`プロパティを介して変更できます。 ディレクトリを指定すると、構造体 `{dir}/{component}/readme.md`が作成されます。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'docs-readme',
      dir: 'output'
    }
  ]
};
```

## strictモード

出力ターゲット構成に `strict:true`を追加すると、ドキュメントが不足しているプロジェクトがビルドされるたびに、Stencilが警告を出力します。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'docs-readme',
      strict: true
    }
  ]
};
```
