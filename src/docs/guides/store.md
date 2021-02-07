---
title: Stencilストア
description: Stencilストア
url: /docs/stencil-store
---

# @stencil/store

[Store](https://github.com/ionic-team/stencil-store)は、ステンシルコアチームによる軽量の共有状態ライブラリです。 必要に応じてコンポーネントを効率的に再レンダリングする単純なキー/値マップを実装します。

 - 軽量
 - 依存関係なし
 - リアクティブマップのようなシンプルなAPI
 - 最高のパフォーマンス

## インストール

```bash
npm install @stencil/store --save-dev
```

## 例

**store.ts:**

```tsx
import { createStore } from "@stencil/store";

const { state, onChange } = createStore({
  clicks: 0,
  seconds: 0,
  squaredClicks: 0
});

onChange('clicks', value => {
  state.squaredClicks = value ** 2;
});

export default state;
```

**component.tsx:**

```tsx
import { Component, h } from '@stencil/core';
import state from '../store';

@Component({
  tag: 'app-profile',
})
export class AppProfile {

  componentWillLoad() {
    setInterval(() => state.seconds++, 1000);
  }

  render() {
    return (
      <div>
        <p>
          <MyGlobalCounter />
          <p>
            Seconds: {state.seconds}
            <br />
            Squared Clicks: {state.squaredClicks}
          </p>
        </p>
      </div>
    );
  }
}

const MyGlobalCounter = () => {
  return (
    <button onClick={() => state.clicks++}>
      {state.clicks}
    </button>
  );
};
```

## API

### `createStore<T>(initialState)`

指定された初期状態で新しいストアを作成します。 この型は `initialState`から推測されるか、ジェネリック型`T`として渡すことができます。

次のプロパティを持つ `store`オブジェクトを返します。

### `store.state`

状態オブジェクトはプロキシされます。 e。 プロパティを直接取得および設定でき、状態オブジェクトが変更されると、Storeがコンポーネントの再レンダリングを自動的に処理します。

注: [`Proxy`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)オブジェクトはIE11ではサポートされていません（ポリフィルを使用している場合でも）。したがって、IE11をサポートする場合は、APIの`store.get`メソッドと `store.set`メソッドを使用する必要があります。

### `store.on(event, listener)`

特定のアクションのリスナーをストアに追加します。

### `store.onChange(propName, listener)`

特定のプロパティが変更されたときに呼び出されるリスナーを追加します。

### `store.get(propName)`

ストアからプロパティの値を取得します。

### `store.set(propName, value)`

ストアにプロパティの値を設定します。

### `store.reset()`

ストアを初期状態にリセットします。

### `store.use(...subscriptions)`

ストアで指定されたサブスクリプションを使用します。 サブスクリプションは、1つ以上のプロパティ `get`、`set`、または `reset`を定義するオブジェクトです。


## テスト

他のグローバル状態ライブラリと同様に、各仕様テストの間に状態をリセットする必要があります。
`beforeEach`フックで`dispose()`APIを使用します。

```ts
import store from '../store';

beforeEach(() => {
  store.dispose();
});
```
