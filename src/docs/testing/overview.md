---
title: Testing
description: Testing overview.
url: /docs/testing-overview
contributors:
  - adamdbradley
  - brandyscarney
  - camwiegert
  - kensodemann
---

# テスト

Stencil内のテストは、単体テストとエンドツーエンド（e2e）テストの2つの異なるタイプに分けられます。 どちらのタイプも、JavaScriptテストソリューションとして[Jest](https://jestjs.io/)を使用します。 エンドツーエンドテスト用のブラウザ環境は、[Puppeteer](https://pptr.dev/)を使用して実行されます。これにより、Stencilがビルドに組み込み始めることができる多くの利点が提供されます。


## ユニットテストとエンドツーエンドテスト

テストをどのように行うべきか、そして単体テスト、エンドツーエンドテスト、さらには統合テストと見なすべきものについては、無数の哲学があります。すべてを単純化するために、ステンシルはそれを分解して、開発者が各タイプのテストをいつ使用するかについて定義された説明を持っているようにします。

**単体テスト**は、コンポーネントのメソッドを分離してテストすることに重点を置いています。たとえば、メソッドに引数 `X`を指定すると、`Y`が返されます。

**エンドツーエンドのテスト**は、コンポーネントがDOMでどのようにレンダリングされるか、および個々のコンポーネントがどのように連携するかに焦点を当てています。たとえば、 `my-component`に`X`属性がある場合、子コンポーネントはテキスト `Y`をレンダリングし、イベント`Z`を受け取ることを期待します。エンドツーエンドのテストでは、ノード環境の代わりに[Puppeteer](https://pptr.dev/)を使用します。これにより、より現実的な結果を提供するために、実際のブラウザー内でエンドツーエンドのテストを実行できます。

JavaScriptテストとDOMテストの境界線を曖昧にしないことで、大規模なチーム間でテストをすばやく構築できます。


## テストコマンド

以下は、アプリの `package.json`ファイルに追加できる`npm`スクリプトの例です。 コマンドが `stencil test`であり、単体テストの場合は` --spec`、エンドツーエンドテストの場合は `--e2e`のオプションのフラグがあることに注意してください。

```tsx
"scripts": {
  "test": "stencil test --spec",
  "test.watch": "stencil test --spec --watch",
  "test.e2e": "stencil test --e2e"
}
```

この構成はすべて、Stencil AppStarterとStencilComponent Starterに含まれているため、これらのテンプレートの1つを使用してプロジェクトを開始する場合は、何も追加する必要はありません。 この情報は、主に情報提供を目的としてここに表示されます。


### 構成のテスト

ステンシルは、すでに収集したデータからデフォルトを適用します。 たとえば、ステンシルは、どのディレクトリを調べるか、どのファイルがspecファイルおよびe2eファイルであるかをすでに認識しています。 Jestは、同じ構成名を使用して構成できますが、ステンシル構成の `testing`プロパティを使用するようになりました。 詳細については、[テスト構成ドキュメント](/docs/config/tests)を参照してください。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  testing: {
    testPathIgnorePatterns: [...]
  }
};
```

## VSCodeでのテストの実行とデバッグ

次の構成を `.vscode/launch.json`に追加すると、VS Code Debuggerを使用して、エディターで現在アクティブなファイルのステンシルテストランナーを実行できます。 実行するテストファイルが表示されていることを確認してから、デバッグ構成をそれぞれ選択し（スペックテストかe2eテストかに応じて）、再生ボタンを押します。

```tsx
{
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "E2E Test Current File",
      "cwd": "${workspaceFolder}",
      "program": "${workspaceFolder}/node_modules/.bin/stencil",
      "args": ["test", "--e2e", "${relativeFile}"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen",
      "disableOptimisticBPs": true
    },
    {
      "type": "node",
      "request": "launch",
      "name": "Spec Test Current File",
      "cwd": "${workspaceFolder}",
      "program": "${workspaceFolder}/node_modules/.bin/stencil",
      "args": ["test", "--spec", "${relativeFile}"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen",
      "disableOptimisticBPs": true
    }
  ]
}
```

## その他の資料

- [StencilJSでのユニットテストの基本](https://www.joshmorony.com/the-basics-of-unit-testing-in-stencil-js/)
