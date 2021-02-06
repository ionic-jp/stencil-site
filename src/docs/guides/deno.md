---
title: Running Stencil with Deno
description: Deno, A secure runtime for JavaScript and TypeScript.
url: /docs/deno
contributors:
  - adamdbradley
---

Traditionally, Stencil and many of today's CLIs for the web ecosystem run on top of [Node](https://nodejs.org/). The Stencil compiler, however, is not locked down to only a Node environment, but rather it can execute from any JavaScript runtime, such as a browser main thread, web worker thread, or the latest JS runtime [Deno](https://deno.land/)! Deno is a simple, modern and secure runtime for JavaScript and TypeScript that uses V8 and is built in Rust.

# Denoランタイム

_`EXPERIMENTAL`：DenoとStencilがどのように機能するかはまだ初期の段階なので、この実験的なラベルを付けます。_

従来、StencilとWebエコシステム用の今日のCLIの多くは、[Node](https://nodejs.org/)上で実行されていました。 ただし、Stencilコンパイラは、ノード環境だけにロックダウンされるのではなく、ブラウザのメインスレッド、Webワーカースレッド、最新のJSランタイムDenoなどのJavaScriptランタイムから実行できます。[Deno](https://deno.land/)、V8を使用し、Rustに組み込まれているJavaScriptおよびTypeScript用のシンプルでモダンで安全なランタイムです。

- [Deno インストレーション](https://deno.land/#installation)
- [Deno マニュアル](https://deno.land/manual)
- [Deno Runtime API](https://doc.deno.land/builtin/stable)

Stencilのアーキテクチャでは、ランタイムと相互作用するシステム（`sys`）を渡すことができます。 たとえば、ノードの `fs`は`readdir()`を使用し、コールバックを渡す必要がありますが、Denoは`readDir()`（大文字のD）を使用してpromiseを返します。 同じ概念がブラウザーとWebワーカーのシステムに適用されます。これにより、コンパイラーは、あらゆるJSランタイムに対して汎用性と汎用性を維持できます。

## Deno StencilCLIのインストール

Stencilコンパイラとそのコマンドラインインターフェイス（CLI）もDenoで実行できますが、従来のノードCLIとは動作が異なります。 最大の違いは、Denoには一元化されたパッケージマネージャーがないため、Denoには同等の`npm`がないことです。

Denoには `npm install`コマンドはありませんが、代わりにインストールする実行可能スクリプトの外部URLを指定します。 [Denoのインストール](https://deno.land/#installation)の後、次のコマンドを実行します。

```bash
deno install -n stencil --allow-read --allow-write --allow-net https://stenciljs.com/cli.ts
```

これをもう少し分解して、このコマンドが何をしているのかを説明しましょう。

| コマンド/引数                  | 説明 |
|--------------------------------|---------------------------------------------------------------------------------|
| `deno`                         | Denoコマンドはすでに[インストール済み](https://deno.land/#installation)です。      |
| `install`                      | [インストーラースクリプト](https://deno.land/manual/tools/script_installer)引数。    |
| `-n`                           | インストールする実行可能ファイルに付ける名前。                             |
| `stencil`                      | `stencil`はこの例では実行可能ファイルの名前ですが、必要に応じてカスタマイズできます。                         |
| `--allow-read`                 | CLIはファイルにアクセスする必要があるため、このオプションを使用すると、CLIでローカルファイルを読み取ることができます。 |
| `--allow-write`                | CLIはファイルを書き込む必要もあります。 |
| `--allow-net`                  | CLIは、依存関係をインストールするには、ネットにアクセスする必要があります。 |
| `https://stenciljs.com/cli.ts` | StencilのDenoインストーラーの場所。 |

心配しないでください。このコマンドは、DenoでStencilを実行するたびに実行する必要はありませんが、代わりにインストールするだけで、マシンに実行可能ファイルの名前「stencil」を付けます。インストール中に、追加のスクリプトファイルがどこから来ているかがわかります。この場合は `https://cdn.jsdelivr.net/npm/`です。

インストール後、グローバルな `stencil`コマンドを使用できるようになり、CLIのhelpコマンドを実行してテストできます。

```bash
stencil help
```

### Deno StencilCLIの更新

インストールされているDenoStencil CLIを更新するには、「-f」オプションを追加して、既存の実行可能ファイルを「強制」上書きし、最新のものを再インストールします。

```bash
deno install -n stencil --allow-read --allow-write --allow-net -f https://stenciljs.com/cli.ts
```


## Deno Stencil CLIの実行

インストーラースクリプトが正常に実行されると、 `stencil`実行可能ファイルをノードCLIと同じオプションで使用できるようになります。 使用可能なすべてのオプションについては、[CLIドキュメント](/docs/cli)を参照するか、`stencil help`を実行してください。

`stencil`は現在グローバルな実行可能ファイルですが、その目標は、個々のアプリケーションのローカルStencilバージョンを実行することを目的としています。 インストールされたコマンドは単なるエントリですが、特定のコンパイラバージョンにロックされていません。 `stencil`コマンドを実行すると、ローカルにインストールされたインストールステンシルコンパイラが使用されているように見えます。まだインストールされていない場合は、最新のステンシルバージョンが動的にロードされます。

_Deno CLIはまだ実験段階であり、generateコマンドはまだ移植されていません。_