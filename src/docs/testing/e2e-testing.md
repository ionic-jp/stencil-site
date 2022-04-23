---
title: E2Eテスト
description: E2Eテスト
url: /docs/end-to-end-testing
contributors:
  - adamdbradley
  - mattdsteele
  - simonhaenisch
---

# エンドツーエンドのテスト

E2Eテストは、実際のブラウザーでコンポーネントを検証します。たとえば、 `my-component`にX属性がある場合、子コンポーネントはテキストYをレンダリングし、イベントZを受信することを期待します。レンダリングテストにPuppeteerを使用することにより（ブラウザの動作をシミュレートするノード環境ではなく）、より良い結果を得るために、エンドツーエンドのテストを実際のブラウザ内で実行できます。

ステンシルは、[Jest](https://jestjs.io/)および[Puppeteer](https://pptr.dev/)のテストに役立つ多くのユーティリティ関数を提供します。たとえば、コンポーネントのシャドウdomは、Puppeteer上に構築されたStencilユーティリティ関数を使用してクエリおよびテストできます。テストは、模擬HTMLコンテンツを提供できるだけでなく、Puppeteerが開いてStencilの開発サーバーでテストできるアプリのURLにアクセスすることもできます。

エンドツーエンドのテストでは、テストを実際に実行する前に、新しいビルド、dev-server、およびpuppeteerブラウザーインスタンスを作成する必要があります。追加されたビルドの複雑さにより、 `stenciltest`コマンドはビルド要件を事前に整理することができます。

E2Eテストを実行するには、 `stencil test--e2e`を実行します。デフォルトでは、 `.e2e.ts`で終わるファイルが実行されます。

ステンシルのE2Eテストは、次のAPIで提供され、 `@stencil/core/testing`から入手できます。
ほとんどのメソッドは非同期であり、Promisesを返します。 `async`と`await`を使用して、テストを整理します。

- `newE2EPage`：新しい `E2EPage`オブジェクトをインスタンス化するには、各テストの開始時に呼び出す必要があります

- `E2EPage`は、テストの作成を簡素化するためのPuppeteerのラッパーユーティリティです。 `E2EPage`のいくつかの便利なメソッドは次のとおりです。

- `setContent(html: string)`：ページのコンテンツを設定します。 これは、テスト対象のコンポーネントのマークアップを含める場所です。
- `find(selector: string)`：セレクターに一致する要素を検索します。 `document.querySelector`に似ています。
- `waitForChanges()`：StencilとPuppeteerの両方に非同期アーキテクチャがあり、これはパフォーマンスにとって良いことです。 すべての呼び出しは非同期であるため、コンポーネントに変更が加えられたときに `await page.waitForChanges()`が呼び出される必要があります。

E2Eテストの例には、次の定型文があります。

```typescript
import { newE2EPage } from '@stencil/core/testing';

describe('example', () => {
  it('should render a foo-component', async () => {
    const page = await newE2EPage();
    await page.setContent(`<foo-component></foo-component>`);
    const el = await page.find('foo-component');
    expect(el).not.toBeNull();
  });
});
```

## E2Eテストの例

```typescript
import { newE2EPage } from '@stencil/core/testing';

it('should create toggle, unchecked by default', async () => {
  const page = await newE2EPage();

  await page.setContent(`
    <ion-toggle class="pretty-toggle"></ion-toggle>
  `);

  const ionChange = await page.spyOnEvent('ionChange');

  const toggle = await page.find('ion-toggle');

  expect(toggle).toHaveClasses(['pretty-toggle', 'hydrated']);

  expect(toggle).not.toHaveClass('toggle-checked');

  toggle.setProperty('checked', true);

  await page.waitForChanges();

  expect(toggle).toHaveClass('toggle-checked');

  expect(ionChange).toHaveReceivedEventDetail({
    checked: true,
    value: 'on'
  });
});
```

## E2Eテストレシピ

#### ShadowDOMで要素を検索する

「ピアス」セレクター `>>>`を使用して、コンポーネントのシャドウルート内のオブジェクトをクエリします。

```typescript
const el = await page.find('foo-component >>> .close-button');
```

#### コンポーネントに@Prop()を設定します

`page。$ eval`（Puppeteer APIの一部）を使用して、Propを設定するか、コンポーネントを操作します。

```typescript
// create a new puppeteer page
// load the page with html content
await page.setContent(`
      <prop-cmp></prop-cmp>
    `);

// select the "prop-cmp" element
// and run the callback in the browser's context
await page.$eval('prop-cmp', (elm: any) => {
  // within the browser's context
  // let's set new property values on the component
  elm.first = 'Marty';
  elm.lastName = 'McFly';
});

// we just made a change and now the async queue need to process it
// make sure the queue does its work before we continue
await page.waitForChanges();
```

#### 外部参照を使用してコンポーネントに@Prop（）を設定します

`page。$ eval`には分離されたスコープがあるため、外部参照を明示的に渡す必要があります。そうしないと、`undefined`エラーが発生します。 これは、別のファイルからデータをインポートしたり、同じファイル内の複数のテストでモックデータを再利用したりする場合に役立ちます。

```typescript
const props = {
  first: 'Marty',
  lastName: 'McFly',
};

await page.setContent(`<prop-cmp></prop-cmp>`);

await page.$eval('prop-cmp',
  (elm: any, { first, lastName }) => {
    elm.first = first;
    elm.lastName = lastName;
  },
  props
);

await page.waitForChanges();
```


#### コンポーネントで@Method()を呼び出す


```typescript
const elm = await page.find('method-cmp');
elm.setProperty('someProp', 88);
const methodRtnValue = await elm.callMethod('someMethod');
```

#### 入力フィールドに入力します

```typescript
const page = await newE2EPage({
  html: `
      <dom-interaction></dom-interaction>
    `
});

const input = await page.find('dom-interaction >>> .input');

let value = await input.getProperty('value');
expect(value).toBe('');

await input.press('8');
await input.press('8');
await input.press(' ');

await page.keyboard.down('Shift');
await input.press('KeyM');
await input.press('KeyP');
await input.press('KeyH');
await page.keyboard.up('Shift');
```

#### レンダリングされたコンポーネントのテキストを確認する

```typescript
await page.setContent(`
      <prop-cmp first="Marty" last-name="McFly"></prop-cmp>
    `);

const elm = await page.find('prop-cmp >>> div');
expect(elm).toEqualText('Hello, my name is Marty McFly');
```

#### コンポーネントのHTMLを確認する

shadowRootコンテンツの場合：

```typescript
        expect(el.shadowRoot).toEqualHtml(`<div>
        <div class=\"nav-desktop\">
          <slot></slot>
        </div>
      </div>`);
    });
```

For non-shadow content:

```typescript
        expect(el).toEqualHtml(`<div>
        <div class=\"nav-desktop\">
          <slot></slot>
        </div>
      </div>`);
    });
```

## CD/CIでのe2eテストの自動化に関する警告

かなり一般的な方法であるため、継続的デプロイ/統合（CD/CI）システムでエンドツーエンドのテストを自動的に実行することをお勧めします。 ただし、環境によっては、構成を微調整する必要がある場合があります。 その場合、 `stencil.config.ts`ファイルの`config`オブジェクトには、パイプラインでヘッドレスChromeが実際にどのように使用されるかを変更するパラメーターを受け入れる `testing`属性があります。

Gitlab CI環境で必要になる可能性のある構成の例：

```typescript
export const config: Config = {
  namespace: 'Foo',
  testing: {
    /**
     * Gitlab CI doesn't allow sandbox, therefor this parameters must be passed to your Headless Chrome
     * before it can run your tests
     */
    browserArgs: ['--no-sandbox', '--disable-setuid-sandbox'],
  },
  outputTargets: [
    { type: 'dist' },
    {
      type: 'www',
    },
  ],
};
```

この問題の可能性について詳しくは、[ドキュメントのこの部分](/docs/config#testing)を確認してください。
