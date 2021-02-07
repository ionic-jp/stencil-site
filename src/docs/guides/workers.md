---
title: Webワーカー
description: Webワーカー
url: /docs/web-workers
---

# Web Workers

**メインスレッド**は、Javascriptがデフォルトで実行される場所であり、 `document`、`window`、およびその他のDOMAPIにアクセスできます。 問題は、JSを長時間実行すると、ブラウザーがスムーズなアニメーション（CSSアニメーション、トランジション、キャンバス、svg ...）を実行できなくなり、サイトがフリーズしたように見えることです。 そのため、アプリケーションでCPUを集中的に使用するタスクを実行する必要がある場合は、Webワーカーが非常に役立ちます。


## Webワーカーを使用するタイミングは？

最初に理解することは、Webワーカーを使用する場合と、一連のコストと制限が伴うためにWebワーカーを使用しない場合です。

- [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)にアクセスできません。つまり、 `document`、`window`、またはページ内の要素を操作することはできません。
- `@stencil/core`APIへのアクセスはありません。たとえば、**DOMへのアクセスがない**のと同じ理由で、WebWorkerでコンポーネントを宣言して使用することはできません。
- 各ワーカーには独自のメモリスペースがあるため、Webワーカーには独自の **分離状態**があります。たとえば、メインスレッドで宣言された変数は、ワーカーから直接参照することはできません。
- ワーカーとメインスレッドの間でデータを渡すときにオーバーヘッドが発生します。原則として、ワーカーとの間で送受信されるデータの量を最小限に抑え、データを送信する作業がメインスレッドで行うよりも時間がかかる場合は注意が必要です。
- 通信は常に**非同期**です。幸いなことに、Promisesとasync/awaitを使用すると、これは比較的簡単になりますが、スレッド間の通信は常に非同期であることを理解することが重要です。
- [プリミティブ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Primitive_values)と[構造化クローンアルゴリズム](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)。それを考える最良の方法は、JSONにシリアル化できるデータが安全に使用できることです。

つまり、一般に、ワーカーを使用して、スレッドブロッキング（またはUIブロッキング）であるロジックをWebワーカーに移動し、リアルタイムのコード構文の強調表示など、ユーザーがページを操作できないようにすることをお勧めします。

## Webワーカーを使用する場合のベストプラクティス

- ワーカーで純粋で機能的なアルゴリズムを使用します。 `(input1, input2)=>output`。
- ワーカーロジック自体は必要なだけ複雑になる可能性がありますが、入力データと出力データはかなり単純なままにする必要があります。
- メインスレッドとワーカースレッド間でのデータの受け渡しを減らす方法を探します。
- クラスインスタンスをデータとして渡すことはできません。 代わりに、データを処理することだけがJSONシリアル化可能になります。
- ワーカー内の状態を最小限に抑えるか、さらに良いことに、状態を維持しないようにします（たとえば、ワーカー内にreduxを入れないでください）。
- CPUを集中的に使用するジョブを実行するため、ワーカーのコストは簡単に償却できます。



## バニラWebワーカーはどのように「機能」しますか？

ブラウザには、次のように機能する `Worker`APIが付属しています。

```tsx
const worker = new Worker('/my-worker.js');
worker.postMessage(['send message to worker']);
worker.onmessage = (ev) => {
  console.log('data from worker', ev.data);
};
```

このAPIは強力ですが、レベルが非常に低く、複雑なアプリを作成するのが面倒です。これは、イベント駆動型パラダイムが[spaghetti-code](https://en.wikipedia.org/wiki/Spaghetti_code)に簡単につながるためです。 厳密に型指定された関数とデータをすぐに見逃します。

詳細については、HTML5Rocksの友人による[この素晴らしいチュートリアル](https://www.html5rocks.com/en/tutorials/workers/basics/)をチェックしてください。

Web Workerでは、上記の例の `my-worker.js`ファイルなどの個別のJavaScriptバンドルも生成する必要があります。 これは通常、ワーカーエントリポイントを別の `.js`ファイルに変換してバンドルする追加のビルドスクリプトとツールが必要であることを意味します。 さらに、メインバンドルは、ワーカーバンドルのファイルの場所を参照できる必要があります。これは、トランスパイル、バンドル、ミニファイ、ファイル名のハッシュ、および本番サーバーへのデプロイ後にしばしば困難になります。

幸い、ステンシルは次の2つの問題の解決に役立ちます。

- ツール：トランスパイル、バンドリング、ハッシュ、ワーカーURLパス参照
- コミュニケーション：タイプを維持しながら、イベントベースのコミュニケーションをPromiseに変換します。

## Stencilを使用したWebワーカー

すでに述べたように、Stencilのコンパイラは、本番環境でワーカーをシームレスに使用するのに役立ちます。 `.worker.ts`で終わる`src`ディレクトリ内のTypeScriptファイルは自動的にワーカーを使用します。

例えば：

**src/stuff.worker.ts:**

```tsx

export const sum = async (a: number, b: number) => {
  return a + b;
}

export const expensiveTask = async (buffer: ArrayBuffer) => {
  for (let i = 0; i < buffer.length; i++) {
    // do a lot of processing
  }
  return buffer;
};
```

**src/my-app/my-app.tsx:**
```tsx
import { Component } from '@stencil/core';

// Import the worker directly.
// Stencil will automatically create
// a proxy and run the module in a worker.
// IDEs and TypeScript will treat this import
// no differently than any other ESM import.
import { sum, expensiveTask } from '../../stuff.worker';

@Component({
  tag: 'my-cmp'
}
export class MyApp {

  async componentWillLoad() {
    // sum() will run inside a worker! and the result is a Promise<number>
    const result = await sum(1, 2);
    console.log(result); // 3

    // expensiveTask() will not block the main thread,
    // because it runs in parallel inside the worker.
    // Note that the functions must be async.
    const newBuffer = await expensiveTask(buffer);
    console.log(newBuffer);
  }
}
```


内部的には、Stencilはワーカーファイルをコンパイルし、標準の `new Worker()` APIを使用してワーカーをインスタンス化します。 次に、エクスポートされた関数ごとにプロキシを作成するため、開発者はイベントベースの関数の代わりに[構造化プログラミング構造](https://en.wikipedia.org/wiki/Structured_programming)を使用してプロキシを操作できます。

> ワーカーはすでに別のチャンクに配置されており、 `new Worker()`を使用して動的にロードされます。 動的な `import()`を使用してそれらをロードすることは避けてください。これにより、2つのネットワーク要求が発生します。 代わりに、ESモジュールのインポートを使用します。これは、ワーカーと通信するためのプロキシのみをインポートするためです。


### ワーカー内でのインポート

Stencilでワーカーを構築する場合、通常の「ESM」インポートが可能です。 内部的には、コンパイラーはワーカーのすべての依存関係を単一のファイルにバンドルします。このファイルは、ワーカーのエントリーポイントになり、問題なく実行できる依存関係のないファイルです。

**src/loader.worker.ts:**

```tsx
import upngjs from 'upng-js';
import { Images } from './materials';

export const loadTexture = async (imagesSrcs: Images) => {
  const images = await Promise.all(
    imagesSrcs.map(loadOriginalImage)
  );
  return images;
}

async function loadOriginalImage(src: string) {
  const res = await fetch(src);
  const png = upngjs.decode(await res.arrayBuffer());
  return png;
}
```

この例では、NPM依存関係（pngファイルの解析に使用される `upngjs`）とローカルモジュール（`./materials`）をインポートする `loader.worker.ts`というワーカーを構築しています。 ステンシルは[ロールアップ](https://rollupjs.org/guide/en/)を使用してすべての依存関係をバンドルし、実行時にすべてのインポートを削除します。 ワーカーの内外にインポートすると、コードが複製されることに注意してください。

#### 動的インポート

ワーカー内でスクリプトを動的にロードするために、Webワーカーには便利なAPI [`importScript()`]（https://developer.mozilla.org/en-US/docs/Web/API/WorkerGlobalScope/importScripts）が付属しています。 ）。

これは、 `importScript()`を使用してCDNから直接 `typescript`を使用する方法の例です。

```tsx
importScripts("https://cdn.jsdelivr.net/npm/typescript@latest/lib/typescript.js");
```

> `npm`または` yarn`を使用してインストールしたNPM依存関係をインポートするために `importScript()`を使用しないでください。 バンドラーが理解できるように、通常のESモジュールインポートを通常どおり使用します。


### ワーカーのコールバック

ほとんどの場合、Promiseが出力データで解決するのを待つだけで十分です。 ただし、ネイティブPromisesの制限は、戻り値が1つしかないことです。 従来のコールバックが依然として優れているのは、さまざまなデータを使用して何度も呼び出すことができることです。

完了までに数秒かかる可能性のある長時間実行プロセスがあるとしましょう。 Promiseでは、Promiseが解決するのを待つだけなので、タスクの進行状況を定期的に受け取ることはできません。

ステンシルのワーカーの機能は、メソッドにコールバックを渡し、ワーカー内で、タスクが解決する前に必要なだけコールバックを実行する機能です。

以下の例では、タスクには指定された番号からカウントダウンする番号が与えられ、タスクは「0」に達すると完了します。 ただし、カウントダウン中も、メインスレッドは毎秒更新を受け取ります。 この例では、ログを「5」から「0」にコンソールします

**src/countdown.worker.ts:**

```tsx
export const countDown = (num: number, progress: (p: number) => void) => {
  return new Promise(resolve => {
    const tmr = setInterval(() => {
      num--;
      if (num > 0) {
        progress(num);
      } else {
        clearInterval(tmr);
        resolve(num);
      }
    }, 1000);
  });
};
```

**src/my-app/my-app.tsx:**
```tsx
import { Component } from '@stencil/core';
import { countDown } from '../countdown.worker';

@Component({
  tag: 'my-cmp'
}
export class MyApp {

  componentWillLoad() {
    const startNum = 5;
    console.log('start', startNum);

    countDown(startNum, (p) => {
      console.log('progress', p);
    }).then(result => {
      console.log('finish', result);
    });
  }
}
```

実行すると、結果は5秒かかり、ログに記録されます。

```
start 5
progress 4
progress 3
progress 2
progress 1
finish 0
```

## Advanced cases

Sometimes it might be necessary to access the actual [`Worker`](https://developer.mozilla.org/en-US/docs/Web/API/Worker) instance, because manual usage of the [`postMessage()`](https://developer.mozilla.org/en-US/docs/Web/API/Worker/postMessage) and [`onmessage`](https://developer.mozilla.org/en-US/docs/Web/API/DedicatedWorkerGlobalScope/onmessage) is desired. However, there's still a tooling challenge in having to bundle the worker, and have the main bundle correctly reference the worker bundle url path. In that case, Stencil also has an API that exposes the worker directly so it can be used instead of the proxies mentioned early.

For a direct worker reference, add `?worker` at the end of an ESM import. This virtual ES module will export:
- `worker`: The actual Worker instance.
- `workerPath`: The path to the worker's entry-point (usually a path to a `.js` file).
- `workerName`: The name of the worker, useful for debugging purposes.

## 高度なケース

[`postMessage()`](https://developer.mozilla.org/en-US/docs/Web/API/Worker/postMessage)と [`onmessage`](https://developer.mozilla.org/en-US/docs/Web/API/DedicatedWorkerGlobalScope/onmessage)を手動で使用する必要があるため、実際の[`Worker`](https://developer.mozilla.org/en-US/docs/Web/API/Worker)インスタンスにアクセスする必要がある場合があります。 ただし、ワーカーをバンドルし、メインバンドルでワーカーバンドルのURLパスを正しく参照する必要がある場合は、ツールに関する課題があります。 その場合、Stencilにはワーカーを直接公開するAPIもあるため、前述のプロキシの代わりに使用できます。

ワーカーを直接参照するには、ESMインポートの最後に `?worker`を追加します。 この仮想ESモジュールは以下をエクスポートします。
- `worker`：実際のWorkerインスタンス。
- `workerPath`：ワーカーのエントリポイントへのパス（通常は `.js`ファイルへのパス）。
- `workerName`：デバッグの目的で役立つワーカーの名前。



**src/my-app/my-app.tsx:**

```tsx
import { Component } from '@stencil/core';
import { sum } from '../../stuff.worker';

// ?workerクエリを使用すると、ワーカーインスタンスに直接アクセスできます。
import { worker } from '../../stuff.worker.ts?worker';

@Component({
  tag: 'my-cmp'
}
export class MyApp {

  componentWillLoad() {
    // ワーカーAPIを直接使用する
    worker.postMessage(['send data manually']);

    // プロキシを使用する
    const result = await sum(1, 2);
    console.log(result); // 3
  }
}
```

この機能を使用して、複数のワーカーを手動で作成することもできます。

```tsx
import { workerPath } from '../../stuff.worker.ts?worker';

const workerPool = [
  new Worker(workerPath),
  new Worker(workerPath),
  new Worker(workerPath),
  new Worker(workerPath),
];
```

この例では、コンパイラによって実行されるバンドルを排他的に利用して、ワーカーのエントリポイントへの `workerPath`を取得し、手動でワーカーのプールを作成します。

>ステンシルは、使用されていない場合はワーカーをインスタンス化しません。これを行うには、ツリーシェイクを利用します。

#### Worker Termination

[`Worker.terminate()`](https://developer.mozilla.org/en-US/docs/Web/API/Worker/terminate)APIを使用して、任意のWebワーカーを終了できますが、Stencilが1つのワーカーを作成するため プロキシされたすべてのメソッドで共有されるため、手動で終了することはお勧めしません。 `terminate`を使用してワーカーを再構築するユースケースがある場合は、`workerPath`を使用して新しいワーカーを直接作成することをお勧めします。

```tsx
import { workerPath } from '../../stuff.worker.ts?worker';
const worker = new Worker(workerPath);
// ...
worker.terminate()
```
