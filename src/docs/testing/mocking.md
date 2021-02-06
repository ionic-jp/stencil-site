---
title: Mocking
description: Mocking
url: /docs/mocking
contributors:
  - simonhaenisch
---

# モック

ステンシルのテスト機能はJestの上に構築されているため、Jestのモック機能を利用して、ライブラリやコードの特定の部分をモックアウトできます。 詳細については、[Jest docs](https://jestjs.io/docs/en/manual-mocks)を参照してください。

## ライブラリのモック

`node_modules`からインポートされたライブラリのモックを作成するには、`node_modules`と同じディレクトリレベル（通常はプロジェクトのルートフォルダ）にフォルダ `__mocks__`を作成し、そこにファイルを作成します。 モックしたいパッケージと同じ名前で、そのモックが自動的に適用されます。

たとえば、 `md5`をモックしたい場合は、次の内容のファイル` __mocks __ /md5.ts`を作成します。

```ts
export default () => 'fakehash';
```

> `@capacitor/core`のようなスコープパッケージをモックしたい場合は、ファイルを `__mocks __/@capacitor/core.ts`として作成する必要があります。

## 自分のコードをあざける

独自のコードのモックを作成するには、別のレイヤーにモックフォルダーを作成する必要があります。

`getRandomInt`ヘルパーを公開するファイル`src/helpers/utils.ts`と、このヘルパーを使用する関数を提供するサービスがあるとします。

```tsx
// src/helpers/utils.ts

export const getRandomInt = (min: number, max: number) =>
  Math.round(Math.random() * (max - min)) + min;
```

```tsx
// src/services/foo.ts

import { getRandomInt } from '../helpers/utils';

export const bar = () => getRandomInt(0, 10);
```

この関数をモックするには、ファイル `src/helpers/__mocks__/utils.ts`を作成し、そのファイルにモックを書き込みます。

```tsx
// src/helpers/__mocks__/utils.ts

export const getRandomInt = () => 42;
```

Jestはノードモジュールのみを自動モックするため、 `jest.mock()`を呼び出して、そのモックを適用することをテストに通知する必要があります。

```tsx
// src/foo.spec.ts

jest.mock('./helpers/utils');

import { bar } from './services/foo';

describe('Foo', () => {
  it('bar()', () => {
    expect(bar()).toBe(42);
  });
});
```

>インポートする前に `jest.mock('...')`を呼び出すことが重要です。

`__mocks__`フォルダーにファイルを作成する代わりに、モックを提供する別のアプローチがあります。`jest.mock()`関数は、オプションの2番目の引数としてモジュールファクトリ関数を取ります。 次のテストは、 `src/helpers/__mocks__/utils.ts`ファイルを作成しなくても、前のテストと同じように機能します。

```tsx
// src/foo.spec.ts

jest.mock('./helpers/utils', () => ({
	getRandomInt: () => 42,
}));

import { foo } from './services/foo';

describe('Foo', () => {
  it('bar()', () => {
    expect(bar()).toBe(42);
  });
});
```

## E2Eテストでのモック

エンドツーエンドのテストで `newE2EPage`を使用する場合、コンポーネントのコードはブラウザコンテキストで実行されます（StencilはPuppeteerを使用してヘッドレスChromiumインスタンスを起動します）。 ただし、モックはNode.jsコンテキストにのみ登録されます。つまり、コンポーネントは引き続き元の実装を呼び出します。 ブラウザのコンテキストで何かをモックする必要がある場合は、[Jest with Puppeteerの使用](https://jestjs.io/docs/en/puppeteer)を参照するか、 `newSpecPage`の使用に切り替えることができます。 ノードコンテキストに仮想（モック）DOMを作成します。

```tsx
// src/components/foo/foo.tsx

import { h, Component, Method } from '@stencil/core';
import { getRandomInt } from '../../helpers/utils';

@Component({ tag: 'foo-component' })
export class Foo {
	@Method()
	async bar() {
		return getRandomInt(0, 10);
	}

	render() {
		return <div />;
	}
}
```

```tsx
// src/foo.e2e.ts

jest.mock('./helpers/utils', () => ({
	getRandomInt: () => 42,
}));

import { newSpecPage } from '@stencil/core/testing';
import { Foo } from './components/foo/foo';

describe('Foo', () => {
	it('bar()', async () => {
		const page = await newSpecPage({
			components: [Foo],
			html: '<foo-component></foo-component>',
		});
		const foo = page.body.querySelector('foo-component');

		if (!foo) {
			throw new Error('Could not find Foo component');
		}

		expect(await foo.bar()).toBe(42);
	});
});
```
