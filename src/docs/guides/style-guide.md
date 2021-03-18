---
title: Stencilのスタイルガイド
description: Stencilのスタイルガイド
url: /docs/style-guide
contributors:
  - jthoms1
  - natemoo-re
  - larionov
  - joestrouth1
---


# Stencil Style Guide

これは、[Ionic Core](https://ionicframework.com/)コンポーネントを標準化する目的で、Stencilのコアチームによって内部的に作成および実施されるコンポーネントスタイルガイドです。 これは、他のチームが独自のスタイルガイドを作成する際の参照としてのみ使用する必要があります。 チームの好みに合わせて自由に変更してください。

>この（またはチームの）スタイルガイドを実施するには、[TSLint](https://palantir.github.io/tslint/)などの静的分析ツールを活用することをお勧めします。 Ionicは[tslint-ionic-rules](https://github.com/ionic-team/tslint-ionic-rules/blob/master/tslint.js)に従います。 オプションで、_communitymaintained_ パッケージ[tslint-stencil](https://www.npmjs.com/package/tslint-stencil)は、特にステンシルコンポーネントを作成するためのルールを提供します。


## ファイル構造

- ファイルごとに1つのコンポーネント。
- ディレクトリごとに1つのコンポーネント。 類似のコンポーネントを同じディレクトリにグループ化することは理にかなっているかもしれませんが、各コンポーネントに独自のディレクトリがあると、コンポーネントを文書化する方が簡単であることがわかりました。
- コンポーネントの実装（.tsx）とスタイルは同じディレクトリに存在する必要があります。

Ionic Coreの例：

```bash
├── my-card
│   ├── my-card.ios.css
│   ├── my-card.md.css
│   ├── my-card.css
│   ├── my-card.tsx
│   └── test
│       └── basic
│           ├── e2e.js
│           └── index.html
├── my-card-content
│   ├── my-card-content.ios.css
│   ├── my-card-content.md.css
│   ├── my-card-content.css
│   └── my-card-content.tsx
├── my-card-title
│   ├── my-card-title.ios.css
│   ├── my-card-title.md.css
│   ├── my-card-title.css
```


## ネーミング
### HTMLタグ

#### プレフィックス
[@ionic/core](https://www.npmjs.com/package/@ionic/core)のように、さまざまなプロジェクトで使用することを目的としたコンポーネントのコレクションを作成する場合、プレフィックスは主要な役割を果たします。 Webコンポーネントは、Webページ内でグローバルに宣言されているため、スコープが設定されていません。つまり、衝突を防ぐために「一意の」プレフィックスが必要です。 プレフィックスは、コンポーネントのコレクションをすばやく識別するのにも役立ちます。 さらに、Webコンポーネントはタグ名内に「-」ダッシュを含める必要があるため、最初のセクションを使用してコンポーネントに名前空間を付けるのが自然です。

Stencilはステンシルコンポーネントを出力しないため、プレフィックスとして「stencil」を使用することはお勧めしません。出力は標準に準拠したWebコンポーネントです。

こんなことしないで：
```markup
<stencil-component>
<stnl-component>
```

代わりに、独自の名前またはブランドを使用してください。 たとえば、[Ionic](https://ionicframework.com/)コンポーネントには、すべて「ion-」というプレフィックスが付いています。
```markup
<ion-button>
<ion-header>
```


#### 名前

コンポーネントはアクションではなく、概念的には「モノ」です。 「animating」の代わりに「animation」など、動詞の代わりに名詞を使用することをお勧めします。 「入力」、「タブ」、「ナビゲーション」、「メニュー」はいくつかの例です。


#### 修飾子

複数のコンポーネントが関連している、または結合されている場合は、名前を共有してから、次のように異なる修飾子を追加することをお勧めします。

```markup
<ion-card>
<ion-card-header>
<ion-card-content>
```


### コンポーネント（TSクラス）

クラスはスコープされているため、コンポーネントのES6クラスの名前にはプレフィックスを付けないでください。 衝突の危険はありません。

```tsx
@Component({
  tag: 'ion-button'
})
export class Button { ... }

@Component({
  tag: 'ion-menu'
})
export class Menu { ... }
```


## TypeScript

1. **フォローする** [tslint-ionic-rules](https://github.com/ionic-team/tslint-ionic-rules/blob/master/tslint.js)

2. **可変デコレータはインライン化する必要があります。**

```tsx
@Prop() name: string;
@Element() el: HTMLElement;
```

3. **メソッドデコレータは複数行である必要があります**

```tsx
@Listen('click')
onClick() {
  ...
}
```

4. **プライベート変数とメソッドを可能な限り使用します。**デッドコードを検出し、カプセル化を実施するのに役立ちます。これはTypeScriptがコードを強化するために提供する機能ですが、 `private`、`public`、または `protected`を使用しても、実際のJavaScript出力に違いはありません。

5. **メソッド/プロップ/イベント/コンポーネントデコレータを含むコードにはjsdocsが必要です：**これにより、ドキュメントの生成が可能になり、TypeScriptインテリセンスを備えたエディターでのユーザーエクスペリエンスが向上します。

## コード編成

**ロバートC.マーチンの _クリーンコード_ からのNewspaperのメタファー**

>ソースファイルは新聞記事のように整理する必要があります。最高レベルの要約が上部にあり、詳細はさらに下にあります。一番上の関数から呼び出された関数はそのすぐ下にあり、以下同様に最下位レベルに、最も詳細な関数が一番下にあります。これは、IDEによって関数の場所の重要性が低くなったとしても、ソースコードを整理するのに適した方法です。これは、関数の出入りが非常に簡単だからです。

### 高レベルの例（コメント付き）

```tsx
@Component({
  tag: 'ion-something',
  styleUrls: {
    ios: 'something.ios.css',
    md: 'something.md.css',
    wp: 'something.wp.css'
  }
})
export class Something {

  /**
   * 1. Own Properties
   * Always set the type if a default value has not
   * been set. If a default value is being set, then type
   * is already inferred. List the own properties in
   * alphabetical order. Note that because these properties
   * do not have the @Prop() decorator, they will not be exposed
   * publicly on the host element, but only used internally.
   */
  num: number;
  someText = 'default';

  /**
   * 2. Reference to host HTML element.
   * Inlined decorator
   */
  @Element() el: HTMLElement;

  /**
   * 3. State() variables
   * Inlined decorator, alphabetical order.
   */
  @State() isValidated: boolean;
  @State() status = 0;

  /**
   * 4. Public Property API
   * Inlined decorator, alphabetical order. These are
   * different than "own properties" in that public props
   * are exposed as properties and attributes on the host element.
   * Requires JSDocs for public API documentation.
   */
  @Prop() content: string;
  @Prop() enabled: boolean;
  @Prop() menuId: string;
  @Prop() type = 'overlay';

  /**
   * Prop lifecycle events SHOULD go just behind the Prop they listen to.
   * This makes sense since both statements are strongly connected.
   * - If renaming the instance variable name you must also update the name in @Watch()
   * - Code is easier to follow and maintain.
   */
  @Prop() swipeEnabled = true;

  @Watch('swipeEnabled')
  swipeEnabledChanged(newSwipeEnabled: boolean, oldSwipeEnabled: boolean) {
    this.updateState();
  }

  /**
   * 5. Events section
   * Inlined decorator, alphabetical order.
   * Requires JSDocs for public API documentation.
   */
  @Event() ionClose: EventEmitter;
  @Event() ionDrag: EventEmitter;
  @Event() ionOpen: EventEmitter;

  /**
   * 6. Component lifecycle events
   * Ordered by their natural call order, for example
   * WillLoad should go before DidLoad.
   */
  connectedCallback() {}
  disconnectedCallback() {}
  componentWillLoad() {}
  componentDidLoad() {}
  componentWillUpdate() {}
  componentDidUpdate() {}
  componentWillRender() {}
  componentShouldRender(newVal: any, oldVal: any, propName: string) {}
  componentDidRender() {}

  /**
   * 7. Listeners
   * It is ok to place them in a different location
   * if makes more sense in the context. Recommend
   * starting a listener method with "on".
   * Always use two lines.
   */
  @Listen('click', { enabled: false })
  onClick(ev: UIEvent) {
    console.log('hi!')
  }

  /**
   * 8. Public methods API
   * These methods are exposed on the host element.
   * Always use two lines.
   * Public Methods must be async.
   * Requires JSDocs for public API documentation.
   */
  @Method()
  async open(): Promise<boolean> {
    // ...
    return true;
  }

  @Method()
  async close(): Promise<void> {
    // ...
  }

  /**
   * 9. Local methods
   * Internal business logic. These methods cannot be
   * called from the host element.
   */
  prepareAnimation(): Promise<void> {
    // ...
  }

  updateState() {
    // ...
  }

  /**
   * 10. render() function
   * Always the last public method in the class.
   * If private methods present, they are below public methods.
   */
  render() {
    return (
      <Host
        attribute="navigation"
        side={this.isRightSide ? 'right' : 'left'}
        type={this.type}
        class={{
          'something-is-animating': this.isAnimating
        }}
      >
        <div class='menu-inner page-inner'>
          <slot></slot>
        </div>
      </Host>
    );
  }
}
```
