---
title: ユニットテスト
description: ユニットテスト
url: /docs/unit-testing
contributors:
  - adamdbradley
  - mattdsteele
  - bassettsj
---

# ユニットテスト

ステンシルを使用すると、[Jest](https://jestjs.io/)を使用してコンポーネントとアプリユーティリティ関数を簡単に単体テストできます。 単体テストは、コードを分離して検証します。 適切に作成されたテストは、高速で再現性があり、簡単に推論できます。

単体テストを実行するには、 `stencil test--spec`を実行します。 `.spec.ts`で終わるファイルが実行されます。


## newSpecPage()

コンポーネントをレンダリングされたHTMLとして単体テストするために、テストでは `@stencil/core/testing`からインポートされた`newSpecPage()`を使用できます。 このテストユーティリティメソッドは `newE2EPage()`に似ていますが、完全な[Puppeteer](https://pptr.dev/)インスタンスを実行する必要がないため、 `newSpecPage()`ははるかに高速です。 Puppeteerを使用した完全なエンドツーエンドテストの詳細については、[newE2EPage()](/docs/end-to-end-testing)ドキュメントを参照してください。

以下は、 `newSpecPage()`にインポートされた1つのコンポーネントクラスと、テストに使用する最初のHTMLが指定されている簡単な例です。 この例では、コンポーネント `MyCmp`がレンダリングするときに、テキストコンテンツを「Success！」として設定します。 次に、マッチャー `toEqualHtml()`を使用して、コンポーネントが期待どおりにレンダリングされるようにします。


```typescript
import { newSpecPage } from '@stencil/core/testing';
import { MyCmp } from '../my-cmp';

it('should render my component', async () => {
  const page = await newSpecPage({
    components: [MyCmp],
    html: `<my-cmp></my-cmp>`,
  });
  expect(page.root).toEqualHtml(`
    <my-cmp>Success!</my-cmp>
  `);
});
```

The example below uses the template option to test the component
```tsx
// mycmp.spec.tsx
// Since the 'template' argument to `newSpecPage` is using jsx syntax, this should be in a .tsx file.
import { h } from '@stencil/core';
import { newSpecPage } from '@stencil/core/testing';
import { MyCmp } from '../my-cmp';

it('should render my component', async () => {
  const greeting = 'Hello World';
  const page = await newSpecPage({
    components: [MyCmp],
    template: () => (<my-cmp greeting={greeting}></my-cmp>),
  });
  expect(page.root).toEqualHtml(`
    <my-cmp>Hello World</my-cmp>
  `);
});

```

### スペックページオプション


`newSpecPage(options)`メソッドは、テストの作成に役立つoptions引数を取ります。


| オプション | 説明 |
|--------|-------------|
| `components` | テストするコンポーネントの配列。コンポーネントクラスはスペックファイルにインポートできます。その後、テスト全体で使用するために、それらの参照を `component`配列に追加する必要があります。 *必須*  |
| `html` | テストの生成に使用される最初のHTML。これは、連携して動作するコンポーネントのコレクションを構築し、HTML属性を割り当てるのに役立ちます。この値は、モックされた `document.body.innerHTML`を設定します。 |
| `template` | The initial JSX used to generate the test. Use `template` when you want to initialize a component using their properties, instead of their HTML attributes. It will render the specified template (JSX) into `document.body`. |
| `autoApplyChanges` | デフォルトでは、コンポーネントのプロパティと属性への変更は、更新をテストするために `page.waitForChanges()`を呼び出す必要があります。オプションとして、 `autoApplyChanges`はバックグラウンドでキューを継続的にフラッシュします。デフォルトは `false` |
| `cookie` | モックされた `document.cookie`を設定します。 |
| `direction` | 模擬の `dir`属性を`<html>`に設定します。 |
| `language` | 模擬の `lang`属性を`<html>`に設定します。 |
| `referrer` | モックされた `document.referrer`を設定します。 |
| `supportsShadowDom` | モックされたドキュメントがシャドウDOMをサポートするかどうかを手動で設定します。デフォルトは `true`  |
| `userAgent` | モックされた `navigator.userAgent`を設定します。 |
| `url` | モックされたブラウザの `location.href`を設定します。 |


### スペックページの結果

`newSpecPage()`から返される「page」オブジェクトには、最初のレンダリングからの初期結果が含まれています。返されるページの結果は「Promise」であることに注意することも重要です。したがって、便宜上、async/awaitを使用することをお勧めします。

ページ結果で最も役立つプロパティは `root`です。これは、ドキュメント内の最初のルートコンポーネントを見つけるのに便利です。たとえば、コンポーネントが多くの `<div>`要素にネストされている場合、クエリセレクタのボイラープレートコードをスキップするために、 `root`プロパティはテスト対象のコンポーネントに直接移動します。


| 結果 | 説明 |
|--------|-------------|
| `body` | 模擬テスト `document.body`。 |
| `doc` | 模擬テスト `document`。 |
| `root` | モックされた `document.body`内で最初に見つかったコンポーネント。コンポーネントが見つからない場合は、 `document.body.firstElementChild`が返されます。  |
| `rootInstance` | コンポーネントインスタンスを返すことを除いて、 `root`と同様です。ルートコンポーネントが見つからなかった場合は、 `null`が返されます。 |
| `setContent(html)` | `document.body.innerHTML`と` waitForChanges() `を設定する便利な関数。関数の引数はhtml文字列である必要があります。 |
| `waitForChanges()` | プロパティや属性の更新など、コンポーネントに変更が加えられた後、テストページは変更を自動的に適用しません。更新を待って適用するには、 `await page.waitForChanges()`を呼び出します。 |
| `win` | 模擬テスト `window`。 |


## コンポーネントクラスロジックのテスト

単純なロジックのみのテストの場合、単体テストでは、クラスをインポートして手動で構築することにより、コンポーネントをインスタンス化できます。 ステンシルコンポーネントはプレーンなJavaScriptオブジェクトであるため、新しいコンポーネントを作成してそのメソッドを直接実行できます。


```typescript
import { MyToggle } from '../my-toggle.tsx';

it('should toggle the checked property', () => {
  const toggle = new MyToggle();

  expect(toggle.checked).toBe(false);

  toggle.someMethod();

  expect(toggle.checked).toBe(true);
});
```
