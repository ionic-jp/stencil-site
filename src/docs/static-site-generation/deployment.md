---
title: Deploying a Static Site
description: Deploying a Static Site
url: /docs/static-site-generation-deployment
contributors:
  - mlynch
---

# ステンシル静的サイトの展開

Stencilで構築された事前レンダリングされた静的サイトのデプロイは、出力が単なるHTMLファイルのセットであるため、静的サイトのデプロイとまったく同じです。

Stencilが検出する（またはプリレンダー構成で `entryUrls`を使用して提供される）すべてのパスは、`www`出力ターゲットのディレクトリに生成され、各URLにはアプリのルートとなる `index.html`が与えられます。

これは、アプリ内のすべてのURLを、アプリ全体をブートストラップするスタンドアロンのWebページに変換することと考えてください。 訪問者がどのURLにアクセスしても、そのページの特定のコンテンツが既にレンダリングされた「index.html」ファイルが提供されますが、アプリ全体がハイドレイトされて読み込まれます。

これは、 `www`出力ターゲットのディレクトリを任意の静的ホストに簡単にデプロイできることを意味します。