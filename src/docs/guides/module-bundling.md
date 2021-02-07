---
title: モジュールのバンドル
description: モジュールのバンドル
url: /docs/module-bundling
contributors:
  - jthoms1
  - adamdbradley
  - kensodemann
  - manucorporat
  - ryan3E0
---

# モジュールのバンドル

ステンシルは、内部で[Rollup](https://rollupjs.org/guide/en/)を使用してコンポーネントをバンドルします。 このガイドでは、発生する可能性のある最も一般的なバンドルの問題のいくつかについて、特定の回避策について説明し、推奨します。

## モジュールごとに1つのコンポーネント

Stencilでコンポーネントを最も効率的にバンドルするには、*TypeScript*ファイルごとに単一のコンポーネント（`@Component`で装飾されたクラス）を宣言する必要があり、コンポーネント自体には一意の`export`が必要です。 そうすることで、ステンシルはアプリ内のコンポーネントグラフ全体を簡単に分析し、コンポーネントをどのようにバンドルするかを最もよく理解できます。 内部的には、Rollupバンドラーを使用して、共有コードを効率的にバンドルします。 さらに、遅延読み込みはStencilのデフォルト機能であるため、コード分割はすでに自動的に行われており、ページで使用されているコンポーネントのみが動的にインポートされます。

コンポーネントを含むモジュールはエントリポイントです。つまり、他のモジュールはそれらから何もインポートしないでください。

次の例は**無効**です。

**src/components/my-cmp.tsx:**
```tsx
// This module has a component, you cannot export anything else
export function someUtilFunction() {
  console.log('do stuff');
}

@Component({
  tag: 'my-cmp'
})
export class MyCmp {}
```

この場合、コンパイラは次のようなエラーを出力します。

```bash
[ ERROR ]  src/components/my-cmp.tsx:4:1
        To allow efficient bundling, modules using @Component() can only have a single export which is the component
        class itself. Any other exports should be moved to a separate file. For further information check out:
        https://stenciljs.com/docs/module-bundling

  L4:  export function someUtilFunction() {
  L5:   console.log('do stuff');
```

解決策は、次のように、共有関数またはクラスを別の `.ts`ファイルに移動することです。

**src/utils.ts:**
```tsx
export function someUtilFunction() {
  console.log('do stuff');
}
```

**src/components/my-cmp.tsx:**
```tsx
import { someUtilFunction } from '../utils.ts';

@Component({
  tag: 'my-cmp'
})
export class MyCmp {}
```

**src/components/my-cmp-two.tsx:**

```tsx
import { someUtilFunction } from '../utils.ts';

@Component({
  tag: 'my-cmp-two'
})
export class MyCmpTwo {}
```


## CommonJSの依存関係

ロールアップは、[ESモジュール（ESM）](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)に依存して、モジュールグラフを適切にツリーシェイクしますが、残念ながら一部のサードパーティのライブラリは、[CommonJS](https://requirejs.org/docs/commonjs.html)モジュールシステムを使用してコードを出荷しますが、これは理想的ではありません。

CommonJSライブラリは今日でも一般的であるため、Stencilには[`rollup-plugin-commonjs`](https://github.com/rollup/rollup-plugin-commonjs)が既にインストールおよび構成されています。

コンパイラ時に、 `rollup-plugin-commonjs`プラグインは**CommonJSをESM**に変換するために最善を尽くしますが、これは必ずしも簡単な作業ではありません。 CommonJSは本質的に動的ですが、ESMは設計上静的です。

Stencilの構成は、RollupCommonJSプラグインに渡される `commonjs`プロパティを公開します。この設定を使用して、特定のバンドルの問題を回避できます。

### NamedModules：XはXによってエクスポートされません

Rollupは、CommonJSモジュールを適切に静的に分析できず、一部の名前付きエクスポートを見逃す場合があります。幸い、使用できる回避策があります。

すでに知っているように、 `stencil.config.ts`は` commonjs`プロパティを公開します。この場合、[`namedExports`プロパティ]（https://github.com/rollup/rollup-plugin-）を利用できます。 commonjs＃custom-named-exports）。

たとえば、 `commonjs-dep`モジュールのexportという名前の` hello`を使用しようとすると、ロールアップが失敗したとします。

```tsx
// NamedModules: hello is not exported by commonjs-dep
import { hello } from 'commonjs-dep';
```

この問題を回避するには、 `stencil.config.ts`ファイルの`config.commonjs.namedExports`設定を使用できます。

```tsx
export const config = {
  commonjs: {
    namedExports: {
       // commonjs-dep has a "hello" export
      'commonjs-dep': ['hello']
    }
  }
}
```

> 問題のある依存関係に対して `namedExports`のマップを設定できます。この場合、`commonjs-dep`モジュールで名前付き `hello`エクスポートを明示的に定義しています。

詳細については、[rollup-plugin-commonjsドキュメント](https://github.com/rollup/rollup-plugin-commonjs)を確認してください。


## カスタムロールアッププラグイン

Stencilは、カスタムロールアッププラグインを `stencil.config.ts`のバンドルプロセスに渡すためのAPIを提供します。 内部的には、ステンシルには `node-resolve`や`commonjs`などの組み込みプラグインが付属しています。一部のロールアッププラグインの実行順序が重要であるため、ステンシルは**ノード解決**の前にカスタムプラグインを挿入するAPIを提供します。 **commonjs変換**後：

```tsx
export const config = {
  rollupPlugins: {
    before: [
      // Plugins injected before rollupNodeResolve()
      resolvePlugin()
    ],
    after: [
      // Plugins injected after commonjs()
      nodePolyfills()
    ]
  }
}
```

経験則として、モジュールを解決する必要のあるプラグインは `before`に配置する必要がありますが、`node-polyfills`、 `replace` ...などのコード変換プラグインは`after`に配置する必要があります。 プラグインのドキュメントに従って、プラグインが正しい順序で実行されていることを確認してください。


## ノードポリフィル

プロジェクトが依存しているライブラリによっては、[rollup-plugin-node-polyfills](https://www.npmjs.com/package/rollup-plugin-node-polyfills)プラグインが必要になる場合があります。 このような場合、ビルド時に次のようなエラーメッセージが表示されます。

```bash
[ ERROR ]  Bundling Node Builtin
           For the import "crypto" to be bundled from 'problematic-dep',
           ensure the "rollup-plugin-node-polyfills" plugin is installed
           and added to the stencil config plugins.
```

これは、ブラウザで使用できない[Node API](https://nodejs.org/dist/latest-v10.x/docs/api/)を使用するサードパーティの依存関係である `rollup-pluginが原因で発生します。 -node-polyfills`プラグインは、ブラウザでこの欠落しているAPIを透過的にポリフィルすることで機能します。

### 1. `rollup-plugin-node-polyfills`をインストールします。

```bash
npm install rollup-plugin-node-polyfills --save-dev
```

### 2. プラグインを含む `stencil.config.ts`ファイルを更新します。

```tsx
import { Config } from '@stencil/core';
import nodePolyfills from 'rollup-plugin-node-polyfills';

export const config: Config = {
  namespace: 'mycomponents',
  rollupPlugins: {
    after: [
      nodePolyfills(),
    ]
  }
};
```

> 注：`rollup-plugin-node-polyfills`はコード変換プラグインであるため、commonjs変換プラグインの後に実行する必要があります。これが、プラグインの「後」配列に配置される理由です。

## strictモード

ESモジュールは常に厳密モードで解析されます。 つまり、特定の非厳密な構成（8進リテラルなど）は、ロールアップがそれらを使用するモジュールを解析するときに構文エラーとして扱われます。 一部の古いCommonJSモジュールはこれらの構造に依存しており、それらに依存するとバンドルが爆発します。 それについて私たちにできることは何もありません。

幸いなことに、すべてに厳密モードを使用しない理由はまったくありません。したがって、この問題の解決策は、これらのモジュールの作成者にロビー活動を行ってモジュールを更新することです。

*ソース: [https://github.com/rollup/rollup-plugin-commonjs#strict-mode](https://github.com/rollup/rollup-plugin-commonjs#strict-mode)*
