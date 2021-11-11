---
title: Extras Config
description: Extras Config
url: /docs/config-extras
contributors:
  - mattdsteele
  - rwaskiewicz
---

# Extras

`extras` には、Stencilの新しい/実験的な機能を有効にするオプションが含まれています。
機能のランタイムを追加したり削除したりするオプションが含まれており、ポリフィルへの操作を必要とします。例えば、Slotポリフィルを使った場合、すべてのDOM APIが完全にポリフィルされるわけではありません。
例えば、Slotポリフィルを使用した場合、全てのDOM APIが完全にポリフィルされるわけではありません。すべてのユーザーが追加のランタイムを必要としているわけではないので、これらのほとんどはオプトインです。

例えば、Slotポリフィルを使用する場合、すべてのDOM APIが完全にポリフィルされるわけではありません。 全てのユーザーが追加のランタイムを必要とするわけではないため、これらのほとんどはオプトインです。

デフォルトでは、StencilはIE11、Edge 18以下（Chromiumに移行する前のEdge）およびSafari 10では機能しません。レガシーブラウザをサポートするには、ブラウザでポリフィルをダウンロードして実行する必要があります。`extras`設定を使用することで、アプリはこれらの追加のランタイム設定をオプトインできます。

例は  __supporting__ レガシーブラウザをサポートする`extras`設定例です:

```tsx
export const config: Config = {
  buildEs5: 'prod',
  extras: {
    cssVarsShim: true,
    dynamicImportShim: true,
    shadowDomShim: true,
    safari10: true,
    scriptDataOpts: true,
    appendChildSlotFix: false,
    cloneNodeFix: false,
    slotChildNodesFix: true,
  }
};
```

注：この例ではレガシーブラウザをサポートする必要があるため、`buildEs5：'prod'`も設定で設定されています。詳細については、[buildEs5 config](/docs/config＃buildes5)を参照してください。

### appendChildSlotFix

Slotポリフィルは、デフォルトでは`appendChild()`を更新しないため、Shadow DOMのように新しい子ノードを正しい子スロットに追加します。これは、必要な人のためのオプトインポリフィルです。

### cloneNodeFix

ランタイムは、デフォルトではSlotポリフィルを使用するコンポーネントを複製するときに、`cloneNode()`をポリフィルしません。これは、必要な人のためのオプトインポリフィルです。

### cssVarsShim

レガシーブラウザでCSSカスタムプロパティを使うためのpolyfillやshimを含めます。レガシービルドのみデフォルトは`true`です。ESMビルドにはcss vars shimは含まれません。これは、レガシービルドのオプトアウトポリフィルです。

これを`false`に設定すると、レガシービルドに"フォールバック"プロパティを手動で提供する必要があります。例えば次のcssでは、IE11のcss variablesはポリフィルされないため、開発者はcss variablesの直前に手動でフォールバックを提供する必要があります。

```css
div {
  color: blue; /* IEで使用される */
  color: var(--color); /* モダンブラウザで使用される */
}
```

### dynamicImportShim

動的な`import()` shimです。これは、Edge18以下およびFirefox67以下でのみ必要です。Edge 18以下（Chromiumに移行する前のEdge）をサポートする必要がない場合は、`dynamicImportShim`を`false`に設定することをお勧めします。デフォルトは `false`です。


### lifecycleDOMEvents

コンポーネントのライフサイクルイベントをディスパッチします。デフォルトではこれらのイベントはディスパッチされませんが、`true`に設定するとこれらのイベントを`window`でリッスンできます。
主にテストで使用されます。

| Event Name                     | Description                                                    |
|--------------------------------|----------------------------------------------------------------|
| `stencil_componentWillLoad`    | 各コンポーネントの`componentWillLoad`に対してディスパッチされます。 |
| `stencil_componentWillUpdate`  | 各コンポーネントの`componentWillUpdate`に対してディスパッチされます。 |
| `stencil_componentWillRender`  | 各コンポーネントの`componentWillRender`に対してディスパッチされます。 |
| `stencil_componentDidLoad`     | 各コンポーネントの`componentDidLoad`に対してディスパッチされます。 |
| `stencil_componentDidUpdate`   | 各コンポーネントの`componentDidUpdate`に対してディスパッチされます。 |
| `stencil_componentDidRender`   | 各コンポーネントの`componentDidRender`に対してディスパッチされます。 |

### safari10

safari 10は`<script type="module">`でESモジュールをサポートしていますが、`<script nomodule>`は実装されていませんでした。`safari10`が`false`に設定されている場合、ランタイムはsafari 10のサポートを適用しません。もしアプリケーションがsafari 10をサポートする必要がない場合は、この値を`false`に設定することをお勧めします。デフォルトは `false`です。

### scopedSlotTextContentFix

An experimental flag that when set to `true`, aligns the behavior of invoking the `textContent` getter/setter on a scoped component to act more like a component that uses the shadow DOM. Specifically, invoking `textContent` on a component will adhere to the return values described in [MDN's article on textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent#description). Defaults to `false`.

### scriptDataOpts

実際の`<script>`要素の`data-opts`プロパティにデータを割り当てることが可能であり、そのデータはStencilの最初の自動実行に渡されます。この機能は非常に特殊なケースでのみ必要とされ、ほとんどの場合必要とされません。`false`に設定すると、このデータは読み込まれません。デフォルトは `false`です。

### shadowDomShim

もし`true`で有効にすると、shadow dom shimが必要かどうかをチェックします。しかし、shadow dom がすでにブラウザでネイティブにサポートされていると判断された場合はshimを要求しません。もし`false`に設定すると、すべての shadow domテストを回避します。アプリが IE11 や Edge 18 以下をサポートする必要がない場合は、`shadowDomShim`を`false`に設定することを推奨します。デフォルトは `false`です。

### slotChildNodesFix

shadow domをサポートしていないブラウザ（IE11とEdge 18以下）では、slotがポリフィルされて同じ動作をシミュレートします。ただし、ホスト要素の`childNodes`と`children`ゲッターは、デフォルトのスロットの子ノードと要素のみを表すようには修正されません。デフォルトは `false`です。
