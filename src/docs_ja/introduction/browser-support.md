---
title: Stencil Web Component Browser Support
description: Out-of-the-box browser support provided by Stencil web components.
url: /docs/browser-support
contributors:
  - adamdbradley
  - kevinports
  - jthoms1
  - arjunyel
---

# ブラウザサポート

Stencilは、主要なデスクトップブラウザ、または、モバイルブラウザで、ネイティブ、または、ほぼネイティブとして動作するWebコンポーネントを構築します。

<div class="bs-chart">
  <div class="bs-chart__group">
    <div class="bs-chart__cards">
      <div class="bs-chart__card">
        <app-icon name="checkmark"></app-icon>
        Chrome 60+
      </div>
      <div class="bs-chart__card">
        <app-icon name="checkmark"></app-icon>
        Safari 10.1+
      </div>
      <div class="bs-chart__card">
        <app-icon name="checkmark"></app-icon>
        Firefox 63+
      </div>
    </div>
    <div class="bs-chart__group-label">
      Full native support
    </div>
  </div>
  <div class="bs-chart__group">
    <div class="bs-chart__cards">
      <div class="bs-chart__card">
        <app-icon name="checkmark"></app-icon>
        Edge 16+
      </div>
      <div class="bs-chart__card">
        <app-icon name="checkmark"></app-icon>
        IE 11
      </div>
    </div>
    <div class="bs-chart__group-label">
      Support with bundled polyfills
    </div>
  </div>
</div>

Webコンポーネントとは、Custom Elements v1の仕様を中心とした、標準化されたブラウザAPIのセットであり、本質的に新しいHTMLタグを定義、および作成するクロスブラウザの方法です。Custom Elements v1は、現在は廃止されたv0仕様の後継です。

カスタムエレメントは、Chrome、Firefox、Safari（iOSを含む）で、サポートされています。Edgeは現在開発中です。

サポートがないブラウザの場合、ポリフィル(小サイズ！)によりカスタムエレメントを利用できます。このポリフィルは、パフォーマンスのオーバーヘッドを最小限に抑えて、シームレスにカスタムエレメントを使用するのに役立ちます。

Stencilは、ダイナミックローダーを使用して、ポリフィルを必要とするブラウザ上でのみ、カスタムエレメントのポリフィルをロードします。このポリフィルで、Stencilは、Chrome（および、すべてのChromeベースのブラウザ）、Safari、Firefox、Edge、IE11をサポートしています。

|                  | Chrome 60+                             | Safari 10.1+                              | Firefox 63+                            | Edge 16+                               | IE 11+                                 |
| ---------------- | :------------------------------------: | :------------------------------------: | :------------------------------------: | :------------------------------------: | :------------------------------------: |
| [CSS Variables](https://caniuse.com/#feat=css-variables)    | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="circle"></app-icon>    |
| [Custom Elements](https://caniuse.com/#feat=custom-elementsv1) | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="circle"></app-icon>    | <app-icon name="circle"></app-icon>    |
| [Shadow Dom](https://caniuse.com/#feat=shadowdomv1)      | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="circle"></app-icon>    | <app-icon name="circle"></app-icon>    |
| [es2017](https://caniuse.com/#feat=async-functions)           | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="circle"></app-icon> |
| [ES Modules](https://caniuse.com/#feat=es6-module)     | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="checkmark"></app-icon> | <app-icon name="circle"></app-icon> |

<div class="align-right">
  <app-icon name="circle"></app-icon> <span class="caption">Stencil compiles with polyfills for features not supported natively</span>
</div>

<style>
  .bs-chart,
.bs-chart__cards,
.bs-chart__card {
  display: flex;
}

.bs-chart {
  margin: 40px 0;
  justify-content: space-between;
}

.bs-chart__group + .bs-chart__group,
.bs-chart__card + .bs-chart__card {
  margin-left: 8px;
}

.bs-chart__group:first-child .bs-chart__card {
  background: #39B54A;
}

.bs-chart__group:last-child .bs-chart__card {
  background: #96D01A;
}

.bs-chart__card {
  width: 117px;
  flex-direction: column;
  align-items: center;
  border-radius: 8px;
  color: #fff;
  padding: 12px;
  font-size: 13px;
  font-weight: 600;
}

.bs-chart__card app-icon {
  background: rgba(255, 255, 255, 0.15);
  padding: 8px;
  border-radius: 100px;
  margin: 6px 0 8px;
}

.bs-chart__card app-icon svg {
  fill: #fff;
}

.bs-chart__group-label {
  display: block;
  text-align: center;
  font-size: 11px;
  color: #646464;
  margin-top: 6px;
}

@media screen and (max-width: 872px) {
  .bs-chart__card {
    width: 100%;
  }

  .bs-chart,
  .bs-chart__group,
  .bs-chart__cards {
    flex-direction: column;
  }

  .bs-chart__group + .bs-chart__group {
    margin-left: 0;
    margin-top: 20px;
  }

  .bs-chart__card + .bs-chart__card {
    margin-left: 0;
    margin-top: 8px;
  }
}
</style>
