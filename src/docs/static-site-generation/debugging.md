---
title: Debugging Static Site Generation in Stencil
description: How to debug a prerendering or Static Site Generation step in Stencil
url: /docs/static-site-generation-debugging
contributors:
  - mlynch
  - adamdbradley
---

# 静的サイト生成のデバッグ

静的サイト生成は、事前レンダリングとも呼ばれ、ビルド時にコンポーネントを実行して、レンダリングされたスタイルとマークアップのスナップショットを生成し、最初のリクエストで検索エンジンとユーザーに効率的に提供します。

このステップはブラウザーではなくNode.jsプロセスで実行されるため、ブラウザーで直接デバッグを行うことはできません。 ただし、既存のNode.jsデバッグ手法を使用すると、デバッグは簡単です。

## 概要

`stencil build --prerender`コマンドは、最初にNodeJS環境のハイドレートスクリプトをビルドし、次にビルドを使用してサイトを事前レンダリングします。 本番ビルドの場合、これはおそらく理想的です。

ただし、デバッグ中は、ハイドレートスクリプトを再構築し続ける必要はなく、事前レンダリングプロセスを通じてデバッグするだけで済みます。 Stencilは、コンポーネントを実際に実行するために使用されるファイルを `dist/hydrate`に作成します。

事前レンダリングのみを行う（そして再構築を回避する）には、スクリプトへのパスをフラグとして使用して、 `stencil prerender dist/hydrate/index.js`コマンドを使用できます。


## 事前レンダリングをデバッグするためのヒント

デフォルトでは、事前レンダリングはホームページをレンダリングすることから始まり、ホームページ内のリンクを見つけ、さらにリンクが見つかるとサイト全体をクロールし続けます。 デバッグ中は、サイト内のすべてのURLを _クロールするのではなく_、1ページだけを事前レンダリングする方が簡単な場合があります。 クロールを無効にするには、事前レンダリング構成 `crawlUrls：false`を設定します。

次に、 `entryUrls`構成を使用して、ホームページから開始するのではなく、事前レンダリングするためのパスの配列を提供できます。

さらに、ランタイム内で出力されるコンソールログは、事前レンダリング中に抑制されます（そうしないと、端末がログで過負荷になります）。 `runtimeLogging：true`を設定すると、ランタイムコンソールのログがターミナルに出力されます。 以下は、プリレンダーデバッグのセットアップ例です。

```tsx
// prerender.config.ts
import { PrerenderConfig } from '@stencil/core';
export const config: PrerenderConfig = {
  crawlUrls: false,
  entryUrls: ['/example'],
  hydrateOptions: _url => {
    return {
      runtimeLogging: true
    };
  }
};
```


## VSコードでのデバッグ

[VS Code's Debugger](https://code.visualstudio.com/docs/editor/debugging)が、ブレークポイントを設定して事前レンダリングプロセスを実行する最も簡単な方法であることがわかりました。

VS Codeで事前レンダリングプロセスをデバッグするには、[デバッグ]タブに入り、次の内容で新しい `launch.json`ファイルを作成します。

```json
// launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Prerender",
      "args": [
        "${workspaceFolder}/node_modules/@stencil/core/bin/stencil",
        "prerender",
        "${workspaceFolder}/dist/hydrate/index.js",
        "--max-workers=0",
        "--config=${workspaceFolder}/stencil.config.ts"
      ],
      "protocol": "inspector",
    }
  ]
}
```

これにより、アプリをハイドレイトするスクリプトを使用して新しいデバッグ構成が作成されます。 `stencil prerender`コマンドを起動し、そこへのパスを提供します
ハイドレートスクリプトが見つかります。 次に、 `--max-workers = 0`を使用しているため、デバッグが困難になるような多数のプロセスを各CPUにフォークしません。
the hydrate script can be found. Next we're using `--max-workers=0` so we do not fork numerous processes to each of your CPUs which will make it difficult to debug.


## その他でのデバッグ

さまざまなツールでデバッグするには、VS Code構成で実行されているコマンドと同様のアプローチに従い、Nodeから `dist/hydrate/index.js`スクリプトを呼び出し、指定された引数を渡し、選択したツールを使用してブレークポイントを設定します ブレークポイント用。


# ブレークポイントとステッピング

事前レンダリング中にコンポーネントをデバッグするには、 `dist/hydrate/index.js`でコンポーネントのトランスパイルされたソースを見つけ、ブレークポイントを設定します（または、元のコンポーネントソースで生の`debugger`ステートメントを使用します）。 事前レンダリングプロセスが実行されると、ブレークポイントに到達し、現在のスタックコンテキストを評価できます。
