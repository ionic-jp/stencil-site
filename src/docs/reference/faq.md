---
title: Stencilのよくある質問
description: Stencilは、再利用可能でスケーラブルなコンポーネントライブラリ、アプリケーション、および設計システムを構築するための開発者向けのツールチェーンです。
url: /docs/faq
contributors:
  - adamdbradley
  - rwaskiewicz
---

# よくある質問

## 前書き

### Stencilとは何ですか？

Stencilは、再利用可能で、スケーラブルなコンポーネントライブラリ、アプリケーション、およびデザインシステムを構築するための開発者向けツールです。高度に最適化されたWebコンポーネントを生成するコンパイラを提供し、最も人気のあるフレームワークの概念をビルドツールに結合します。

Stencilは、Web標準でコンポーネントを構築することに重点を置いています。世界中の開発者や組織で使用されており、[100%フリーでMITオープンソース](https://github.com/ionic-team/stencil/blob/main/LICENSE.md)となっています。

### Stencilは何をしますか？

Stencilは、開発者とチームがカスタムコンポーネントを構築して共有するのに役立ちます。Stencilは標準に準拠したWebコンポーネントを生成するため、Stencilを使用して構築したコンポーネントは、すぐに使用できる多くの一般的なフレームワークで動作し、Webコンポーネントであるため、フレームワークなしでも使用できます。Stencilはまた、Webコンポーネントに加えて、特に事前レンダリングや、(文字列だけでなく)プロパティとしてのオブジェクトなど、いくつかの重要な機能を有効にします。


### Stencilは誰のためのものですか？

Stencilは、チーム、フレームワーク、および大規模な組織間で共有できるカスタムコンポーネントライブラリとデザインシステムを構築したい開発者とチーム向けです。

Stencilは、元の設計ビジョンをすべてのユーザーに忠実に一貫して提供したい設計者も使用できます。


### 誰がStencilを作っていますか?

Stencilは、[Ionic core team](https://ionicframework.com/)によって開始されたオープンソースプロジェクトです。コミュニティによる貢献もあります。


### Stencilが作成されたのはなぜですか？

Stencilは、独自のコンポーネントライブラリをより速く、小さく、すべての主要なフレームワークと互換性を持たせるために、Ionicチームによって作成されました。Webコンポーネントは、パフォーマンスを向上させるためにブラウザーより多くの作業を行い、すべてのフレームワークが使用できる標準ベースのコンポーネントモデルをターゲットにすることでソリューションを提供しました。


### Stencilを使用するのは誰ですか？

Stencilは当初IonicFramework用に開発され、非常に成功したWebコンポーネントベースのデザインシステム/UIフレームワークです。現在、Webコンポーネントは、数千のアプリに含まれており、毎年400万近くの新しいIonicを使ったプロジェクトが作成されています。


### Stencilは従来のフレームワークとどのように比較されますか？

Webコンポーネントのエコシステムには多様なプレーヤーのセットがあり、それぞれがWebコンポーネントが実行できることと実行すべきことについて異なる長期ビジョンを持っています。

Webコンポーネントは、サードパーティのアプリフレームワークに取って代わるべきだと考える人もいれば、Webコンポーネントは、リーフ/スタイル/デザインノードに適しているため、アプリのコンポーネントシステムのビジネスに参入すべきではないと考える人もいます。また、Webコンポーネントの要点を理解していない、またはフロントエンドのイノベーションを侮辱しているとみなしているフレームワーク開発者も多数います。

Stencilでは、私達のビジョンは真ん中のどこかにあります。長期的には、アプリ開発チームは選択したフレームワークを引き続き使用します。これらのフレームワークは、Webコンポーネントのターゲティングと消費に対するサポートがますます向上し、より良く、より小さく、より効率的になることを想定しています。企業が共有デザインシステムにそれらを採用し続けるにつれて、大規模なチームはますます多くのWebコンポーネントを消費するようになります。

同時に、Webコンポーネントに不可欠な機能は、これらのコンポーネントの配布とデザインシステムの問題を解決することであると考えています。ただし、市場の90％にはそもそもこれらの問題がないため、Webコンポーネントのメリットに関する現在の議論はやや非生産的であると考えています。

At the same time, we believe an indispensable feature for Web Components is solving those component distribution and design system problems. We also believe, however, that 90% of the market doesn’t have those problems to begin with, so the current debate about the merits of Web Components is somewhat unproductive.

### Stencilがフレームワークにとらわれないとみなされるのはなぜですか？

### Why is Stencil considered framework-agnostic?


## Stencilは何を提供しますか?

### Stencilにはコンポーネントライブラリがありますか？


Stecnilを使ったコンポーネントライブラリで、最も広く使用されているのはIonicですが、ステンシル自体は単なるツールチェーンであり、独自のコンポーネントライブラリを提供していません。 アプリケーションを構築する場合は、最初にIonicコンポーネントを確認することをお勧めします。


### Stencilはフレームワークですか？

Stencilは意図的にスタンドアロンフレームワークとして機能するように努めるのではなく、開発者が多くのプロジェクト、チーム、および大規模な組織にわたってフレームワークに依存しないコンポーネントを拡張できるようにするツールです。 Stencilの強力なパワーの1つは、その柔軟性です。そのコンポーネントは、スタンドアロンで使用することも、従来のフレームワーク内で使用することもできます。


### Stencilにはテストフレームワークが付属していますか？

はい、ステンシルはユニットテストとエンドツーエンドテスト用の豊富なAPIセットを提供します。[Stencilを使用したテストの詳細](/docs/testing-overview).



## 技術


### StencilがWebコンポーネントを使用するのはなぜですか?

一貫性のあるWeb標準のセットを使用することにより、Webコンポーネントは実行する特定のフレームワークランタイムに依存しません。フレームワークがAPIを変更しても、Webコンポーネントは変更しないため、元のソースは引き続きブラウザーでネイティブに機能します。

結局のところ、今日のホットなフレームワークが大好きなのと同じくらい、明日が何をもたらすか誰が知っているのでしょうか。 Webコンポーネントを選択することで、テクノロジーチャーンの脅威から身を守ることができます。

### Stencilはどのようにしてコンポーネントのファイルサイズと起動を最適化できますか？

従来のフレームワークはランタイムAPIを提供し、開発者はコンポーネントごとに使用するAPIを選択できます。ただし、これは、コンポーネントがAPIを使用する場合と使用しない場合に備えて、すべての機能をすべてのコンポーネントで使用できるようにする必要があることを意味します。

Stencilを使用すると、コンパイラーは、使用されているAPIと使用されていないAPIを理解するために、各コンポーネントで静的分析を実行できます。そうすることで、Stencilは各ビルドをカスタマイズして、各コンポーネントが必要とするものを正確に使用できるようになり、最小サイズで高度に最適化されたランタイムを実現します。

Stencilはコンパイラを使用するため、新しい改善や機能が利用可能になったときにコードを調整できます。ソースコードは、同じパブリックAPIと構文を使用して引き続き記述できますが、コンパイラーは、再記述を必要とせずに、コードを調整して最新の機能をさらに活用できます。

### Stencilはどのテンプレート構文を使用しますか？

Stencilは、文書化して教える必要のあるさらに別のテンプレート構文を発明するのではなく、間違いなく最も一般的に使用されるテンプレート構文であるJSXを選択しました。 Reactの人気により、世界中の何百万もの開発者がすでにJSXに精通しており、開発者がStencilをすばやく簡単に入手できるようになっています。 さらに、従来のランタイムテンプレート構文では、APIを変更すると、コンポーネントの書き直しが必要になることがよくあります。


JSXとVDomは必ずしも同じものではないことに注意することが重要です。 1つはテンプレート構文（JSX）で、もう1つはレンダラー（VDom）です。 Stencilははるかに小さく高度に最適化されたVDomを使用しますが、レンダラーの「動作」と行われる改善はJSXの背後にあります。 これはすべてコンパイラの利点であり、ユーザーは一般的に知られているJSX構文でコードを記述できると同時に、内部をさらに最適化できます。


### Stencilでコンポーネントの遅延読み込みが許可されるのはなぜですか？

遅延読み込みコンポーネントは、アプリケーションの起動時間を短縮し、バンドルサイズを縮小し、配布を改善するのに役立ちます。

たとえば、Ionic Frameworkには、すべてStencilで構築された100近くのUIコンポーネントが含まれています。遅延読み込みがない場合、Ionicのエントリファイルは約800kbに​​なります。遅延読み込みを使用すると、すべてのIonicのスクリプトはわずか5kbです。これには、iOSとマテリアルデザインの両方、およびアニメーションとジェスチャーライブラリのコンポーネントが含まれます。 Ionicは使用されているものだけを動的にロードできるため、起動時間が大幅に短縮され、ユーザーはアプリケーションの最初のペイントに必要なものだけを正確にロードします。

同時に、Stencilで構築されたコンポーネントは、従来のバンドラーでインポートおよび使用できます。また、事前にレンダリングしてShadow DOMを含めたり、ノード環境で実行したり、任意のフレームワーク内で使用したりすることもできます。

コンポーネントライブラリの利用者は、1つのコンポーネント、いくつかのコンポーネント、またはすべてのコンポーネントを使用できます。これらのシナリオのいずれにおいても、スクリプトタグを追加するだけでコンポーネントライブラリが使用され、遅延読み込みにより、低帯域幅のフットプリントで高速な起動が保証されます。

遅延読み込みの詳細については、[遅延読み込みWebコンポーネントがStencilでどのように機能するか](/blog/how-lazy-loading-web-components-work)でも確認できます。

You can also learn more about lazy loading in [How Lazy-Loading Web Components Work with Stencil](https://web.archive.org/web/20201108000809/https://stenciljs.com/blog/how-lazy-loading-web-components-work).

### StencilがHTMLElementを拡張しないのはなぜですか？

Webコンポーネント仕様の一部はHTMLElementを拡張することですが、Stencilはいくつかの理由でこれを必要としません。 具体的には、HTMLElementは、ロジックをAPIから解放するのではなく、ブラウザでのみ機能するようにソースコードをロックします。 Stencilコンパイラは、さまざまな出力ターゲット用にさまざまなバージョンのコンポーネントを最適化できます。これを簡単にする1つの方法は、HTMLElementを除外することです。

Part of the Web Component spec is to extend HTMLElement, however, Stencil does not require this for a few reasons. Specifically, HTMLElement locks source code to only work in the browser, rather than keeping the logic free from any APIs. The Stencil compiler is able to optimize many versions of components for various output targets, and one way to make that easier is by keeping HTMLElement out of it.

### StencilコンポーネントがTypeScriptで記述されているのはなぜですか？

StencilはもともとIonic用に構築されたものであり、私たちの経験では、TypeScriptは複数のチームにわたって大規模なコードベースを維持するための貴重なツールであることがわかりました。


### Stencilランタイムにはどのような依存関係がありますか？

無し。 Stencilによって生成されるコードは、Stencilに依存しませんが、ブラウザーでネイティブに実行される、高度に最適化された、フレームワークのないスタンドアロンコードを生成します。


### Stencilコンパイラにはどのような依存関係がありますか？

TypeScript。 それでおしまい。


### データをWebコンポーネントに渡すことはできますか？

Webページの他のDOM要素と同様に、配列、オブジェクト、文字列、数値の形式のデータを要素のプロパティに渡すことができます。 Stencilは、アプリケーション開発者がこの機能のロックを解除したままになるようにゼロから設計されています。

文字列のみをカスタム要素に渡すことができると誤って主張する1つの誤解は、ReactのバージョンのVDomに由来します。 箱から出して、Reactは文字列と数値のみをコンポーネントに渡すことができ、カスタムイベントをリッスンすることはできません。 ステンシルを使用すると、コンポーネントはReactコンポーネントであるかのように表示され、関数、オブジェクト、配列を含むすべてのプロパティが正しく渡されます。 バインディングは、「on<EventName>」という小道具を作成することにより、カスタムイベントも考慮します。 これらにより、React開発者はReactコンポーネントであるかのようにWebコンポーネントと対話できます。

One misconception that falsely claims only strings can be passed to custom elements comes from React’s version of VDom. Out of the box, React can only pass strings and numbers to components, and it cannot listen to custom events. With Stencil, the components appear as though they are React components and all properties get passed correctly including functions, objects, and arrays. The bindings also account for custom events by creating a prop called “on<EventName>”. These allow React developers to interact with the Web Components as though they are React components.

### Stencilはどのテクノロジーで構築されていますか？

StencilコンパイラはTypeScriptで構築されており、[npmで配布](https://www.npmjs.com/package/@stencil/core)されています。 そのディストリビューションにはタイプが含まれているため、開発者はStencilAPIを簡単に使用できます。


## 機能

### Stencilコンポーネントはどこで使用できますか？

Webコンポーネントを使用する大きな利点の1つは、コンポーネントライブラリがデスクトップWebアプリだけでなく、すべてのプロジェクトで機能することです。

たとえば、Ionicのようなハイブリッドモバイルフレームワークを使用すると、ネイティブiOSおよびAndroidアプリから、ElectronおよびデスクトップWebアプリ、さらにはプログレッシブWebアプリまで、ほぼすべてのプラットフォームまたはデバイスにWebコンポーネントをデプロイできます。


### Webコンポーネントの制限は何ですか？

[Webコンポーネント](https://developer.mozilla.org/en-US/docs/Web/Web_Components)の仕様は意図的に低レベルであり、それ自体ではフレームワーク品質の開発者エクスペリエンスを提供しません。 Webコンポーネントはかなり原始的な一連の標準で実行されるため、目的を達成するために少しの助けが必要になります。

いくつかの制限は次のとおりです。

Some limitations include:

さらに、Webコンポーネントは技術的にはどのフレームワークでも機能しますが、上記のように、型のサポートや入力バインディングの欠如、コンポーネントへのプロパティの受け渡しなど、いくつかの制限があります。

良いニュースは、Stencilのようなオープンソースツールの助けを借りて、これらすべての課題を克服できることです。 Stencilには、Angular、React、Vueのフレームワークバインディングが含まれているため、Webコンポーネントライブラリを任意のフレームワークに簡単にインポートして、そのフレームワークにネイティブであるかのように、使い慣れたすべての機能を使用してそれらを操作できます。


### フレームワークバインディングとは何ですか？

Webコンポーネントは任意のJavaScriptフレームワークと組み合わせることができますが、Stencilには、Angular、React、およびVueでアプリケーションを構築するときにエンタープライズチームが期待するより高度な機能を提供するための特別な目的のバインディングが組み込まれています。


### StencilはWebコンポーネントにどのような機能を追加しますか？

Webコンポーネントだけでは、高品質の開発エクスペリエンスを提供するのに十分ではありませんでした。 高速なWebアプリを構築するには、以前は従来のWebフレームワーク内に閉じ込められていたイノベーションが必要でした。 Stencilは、これらの機能を従来のフレームワークから引き出し、急速に出現しているWebコンポーネント標準に組み込むために構築されました。

Web Components by themselves weren't enough to provide a quality development experience. Building fast web apps required innovations that were previously locked up inside traditional web frameworks. Stencil was built to pull these features out of traditional frameworks and bring them to the fast emerging Web Component standard.


### どのブラウザがStencilコンポーネントをサポートできますか？

Stencilは最新のブラウザとInternetExplorer11で動作します。

[ブラウザサポートの詳細](/docs/browser-support).


### StencilはどのようなPolyfillを提供しますか？

StencilをInternetExplorer 11以降で動作させるには、いくつかのポリフィルが必要です。 幸い、Stencilのアーキテクチャにより、最新のブラウザでポリフィルをダウンロードする必要がなくなります。

さらに、最新のブラウザーは、ES5にトランスパイルすることなく（コードのデバッグをさらに簡単にすることなく）、最新の機能をネイティブに使用できます。 たとえば、async / awaitなどのすべてのES2017機能はネイティブに実行されます。


## Stencilプロジェクト

### どこでサポートを受けられますか？

デザインシステムを構築するのが初めての場合、またはStencilを初めて使用する場合は、ソリューションエンジニアの1人に、目標を達成し、プラットフォームを最大限に活用する方法について[相談](https://ionicframework.com/sales?product_of_interest=Design%20Systems)ください。。

### How do I get involved?

Stencil is an open source project, and we encourage you to contribute. You can start by creating issues on GitHub, submitting feature requests, and helping to replicate bugs. If you’re interested in contributing, please see our [Contributor Guide](https://github.com/ionic-team/stencil/blob/main/.github/CONTRIBUTING.md) and check out our [issue tracker](https://github.com/ionic-team/stencil/issues).

はい、Stencilはオープンソースであり、そのソースコードは[GitHubにあります](https://github.com/ionic-team/stencil)です。コミュニティからの貢献を歓迎します。


### Which software license does Stencil use?

Stencil’s software [license is MIT](https://github.com/ionic-team/stencil/blob/main/LICENSE.md).


### Who works on Stencil?

The majority of the development is done by engineers at [Ionic](https://github.com/ionic-team/ionic). If you’re excited about Stencil, we encourage you to join the community and contribute! Best place to start is on the [Stencil Slack Channel](https://stencil-worldwide.herokuapp.com/).

開発の大部分は、[Ionic](https://github.com/ionic-team/ionic)のエンジニアによって行われます。Stencilに興奮している場合は、コミュニティに参加して貢献することをお勧めします。開始するのに最適な場所は、[Stencil Slack Channel](https://stencil-worldwide.herokuapp.com/)です。
