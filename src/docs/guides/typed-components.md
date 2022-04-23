---
title: 型付きコンポーネント
description: 型付きコンポーネント
url: /docs/typed-components
contributors:
  - adamdbradley
  - manucorporat
---

# 型付きコンポーネント

Stencilで生成されたWebコンポーネントには、ステンシルコンパイラによって自動的に生成された入力情報が付属しています。 Typescript宣言は、コンポーネントを消費するときに強力な保証を提供します。

- 適切な値がプロパティとして渡されるようにする
- AtomやVSCodeなどの最新のIDEでのコードのオートコンプリート
- イベントの詳細
- コンポーネントのメソッドの署名

このパブリックタイプは、Stencilによって `src/component.d.ts`で自動的に生成されます。これにより、JSX（Reactと同様）および各コンポーネントの`HTMLElement`インターフェイスでの強い型付けが可能になります。

Stencilによって生成されたWebコンポーネントは単なるバニラWebコンポーネントであるため、 `HTMLElement`インターフェイスを拡張します。 コンポーネントごとに、 `HTML{CamelCaseTag}Element`という名前のタイプがグローバルスコープに登録されます。つまり、`HTMLElement`や `HTMLScriptElement`がインポートされないように、開発者はそれらを明示的にインポートする必要はありません。

- `ion-button` => `HTMLIonButtonElement`
- `ion-menu-controller` => `HTMLIonMenuControllerElement`

```tsx
const button: HTMLIonButtonElement = document.queryElement('ion-button');
button.fill = 'outline';
```

**重要**：コンポーネントへの参照を保持するために、常に `HTML{}Element`インターフェースを使用してください。

## Properties

This section has moved to [Property Types](/docs/properties#types)

### Required Properties

This section has moved to [Required Properties](/docs/properties#required-properties)
