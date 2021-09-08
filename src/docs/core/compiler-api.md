---
title: Stencil Core Compiler API
description:  Stencil Core Compiler API
url: /docs/compiler-api
contributors:
  - adamdbradley
---

# ステンシルコアコンパイラAPI

コンパイラAPIは `@stencil/core/compiler/stencil.js`にあります。 このモジュールは、NodeJS環境、Webワーカー、およびブラウザーウィンドウ内で機能します。 `stencil.min.js`ファイルも提供されており、ブラウザ内で使用する場合に推奨されます。

```tsx
// NodeJS (commonjs)
const stencil = require('@stencil/core/compiler');

// Web Worker from CDN URL (add the version to in the URL)
importScripts('https://cdn.jsdelivr.net/npm/@stencil/core@[VERSION]/compiler/stencil.min.js');
// self.stencil will be available after the script import

// Browser Window
<script src="https://cdn.jsdelivr.net/npm/@stencil/core@[VERSION]/compiler/stencil.min.js"></script>
// window.stencil will be available after the script executes
```


## transpile()

```tsx
transpile(code: string, opts?: TranspileOptions): Promise<TranspileResults>
```

`transpile()`関数は、2番目の引数内にさまざまなオプションを指定して、ソースコードを文字列として入力します。 この関数はステートレスであり、診断やトランスパイルされたコードを含む結果の「約束」を返します。 `transpile()`関数は、SassやLessなどのCSS前処理のバンドル、ミニファイ、またはプリコンパイルを処理しません。

同等の `transpileSync()`が使用できるため、同じ関数を同期的に呼び出すことができます。 ただし、TypeScriptが機能するには、グローバル内にすでにロードされている必要がありますが、非同期の `transpile()`関数はTypeScriptを自動的にロードします。

TypeScriptが使用されるため、ソースコードはTypeScriptからJavaScriptにトランスパイルされ、Babelプリセットは必要ありません。 さらに、結果には、ソースファイルで見つかったすべてのインポートパスの `imports`配列が含まれます。 トランスパイルオプションを使用して、 `cjs`などの`module`形式、および `es2017`などのJavaScriptの`target`バージョンを設定できます。


## transpileSync()

```tsx
transpileSync(code: string, opts?: TranspileOptions): TranspileResults
```

`transpile()`関数と同期的に同等です。 ブラウザ環境で使用する場合、TypeScriptはすでにグローバルに利用可能である必要がありますが、非同期の `transpile()`関数はTypeScriptを自動的にロードします。

## createCompiler()

```tsx
createCompiler(config: Config): Promise<Compiler>
```

コンパイラは、トランスパイラー、バンドラー、ミニファイアなど、最適化されたコンポーネントを構築するための多くのツールをまとめたユーティリティです。 CLIを使用する場合、 `stencil build`コマンドは、本番ビルドや開発中の監視モードなど、さまざまなビルドにコンパイラを使用します。 1つのファイルのみをトランスパイルする必要がある場合（ソースコードをTypeScriptからJavaScriptに変換する場合）、代わりに `transpile()`関数を使用する必要があります。

ステンシル構成を指定すると、このメソッドは非同期で `Compiler`インスタンスを返します。 提供されている構成は、 `loadConfig（{...}）`メソッドを使用してすでに作成されているはずです。

以下は、フルビルドを実行しているNodeJS環境の例です。

```tsx
import { createNodeLogger, createNodeSystem } from '@stencil/core/cli';
import { createCompiler, loadConfig } from '@stencil/core/compiler';

const logger = createNodeLogger(process);
const sys = createNodeSystem(process);
const validated = await loadConfig({
  logger,
  sys,
  config: {
    /* user config */
  },
});
const compiler = await createCompiler(validated.config);
const results = await compiler.build();
```


## createSystem()

```tsx
createSystem(): CompilerSystem
```

コンパイラは `CompilerSystem`インスタンスを使用して、ファイルシステムの読み取りと書き込みにアクセスします。 CLIから使用する場合、CLIはNodeJSに基づく独自のシステムを提供します。 このメソッドは、コンパイラシステムがメモリ内のみであり、プラットフォームに依存しないことを提供します。


## dependencies

```tsx
dependencies: CompilerDependency[]
```

`dependencies`配列は情報提供のみを目的としており、コンパイラが構築されて動作する依存関係のバージョンを示すために提供されています。 たとえば、このバージョンのステンシルに使用されているTypeScript、Rollup、Terserのバージョンをここに示します。


## loadConfig()

```tsx
loadConfig(init?: LoadConfigInit): Promise<LoadConfigResults>
```

`loadConfig(init)`メソッドは、生の構成情報を取得し、それをコンパイラーと開発サーバーで使用可能な構成オブジェクトに変換するために使用されます。 `init`引数には、コンパイラでも使用できる、作成済みのシステムとロガーを指定する必要があります。


## optimizeCss()

```tsx
optimizeCss(cssInput?: OptimizeCssInput): Promise<OptimizeCssOutput>
```

CSSを最適化するためにコンパイラーが使用するユーティリティ関数。


## optimizeJs()

```jsx
optimizeJs(jsInput?: OptimizeJsInput): Promise<OptimizeJsOutput>
```

JavaScriptを最適化するためにコンパイラーが使用するユーティリティ関数。 JavaScriptターゲットを知っていると、通常のミニファイを超えてミニファイ最適化がさらに適用されます。


## path

```tsx
path: PlatformPath
```

NodeJSによって提供される `path` APIのユーティリティですが、どの環境でも実行できます。 この `path` APIはPOSIXバージョンのみです：https://nodejs.org/api/path.html


## version

```tsx
version: string
```

Current version of `@stencil/core`.
