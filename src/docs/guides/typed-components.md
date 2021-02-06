---
title: Typed Components
description: Typed Components
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


## プロパティ

コンポーネントのプロパティと属性は、インスタンス変数に注釈を付けることで定義されます。
`@Prop()` デコレータ:

```tsx
@Prop() prop: number;
```

Stencilはまた、**タイプ情報**を使用してWebコンポーネントタイプを生成し、実行時に指定されたタイプに値を自動的にキャストします:

```tsx
@Prop() str1: string; // string attribute
@Prop() str2: 'md' | 'ios'; // string attribute
@Prop() str3 = 'defaultValue'; // string attribute

@Prop() number1: number; // numeric attribute
@Prop() number2 = 42; // numeric attribute

@Prop() boolean1: boolean; // boolean attribute
@Prop() boolean2 = true; // boolean attribute
```


### 必要なプロパティ

変数名の後に `!`を使用すると、この属性/プロパティが必要に応じてマークされます。これにより、JSXタイプが確実になります。
プロパティは渡されます。

```tsx
@Prop() value!: string;
```
