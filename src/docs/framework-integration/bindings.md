---
title: Framework bindings
description: Framework bindings
url: /docs/framework-bindings
contributors:
  - manucorporat
---

# フレームワークバインディング

残念ながら、Webコンポーネントを既存のアプリケーションに統合する経験は、時には注意が必要です。これについての詳細は、[https://custom-elements-everywhere.com/](https://custom-elements-everywhere.com/)で読むことができます。さまざまな問題に対応するために、Stencilチームは、プロセスを簡素化するための新しい出力ターゲットプラグインを作成しました。

プラグインは、含まれているフレームワークバインディングごとに追加の出力ターゲットを追加します。この出力ターゲットは、コンポーネントがこれらのフレームワークのいずれかを使用して最初に作成されたかのように、ネイティブのangular/react/vueライブラリを出力します。

ステンシルバインディングを使用すると、コンポーネントを1回ビルドでき、ステンシルはangular/react/vueライブラリを出力します。これにより、コンポーネントのコンシューマーはすべてのフレームワーク機能を楽しむことができます。

参照用のプラグインを使用したプロジェクトリポジトリの例を次に示します。 https://github.com/ionic-team/stencil-ds-plugins-demo

- [Angularバインディング](/docs/angular#bindings)
- [Reactバインディング](/docs/react#bindings)


