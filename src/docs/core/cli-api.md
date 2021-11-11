---
title: Stencil Core CLI API
description:  Stencil Core CLI API
url: /docs/cli-api
contributors:
  - adamdbradley
---

# ステンシルコアCLI API

CLI APIは `@stencil/core/cli`にあり、`bin/stencil`によって実行されます。

## createNodeLogger()

```tsx
createNodeLogger(process: any): Logger
```

NodeJS APIに基づいて、コンパイラーとdev-serverによって使用される「ロガー」を作成します。 デフォルトでは、CLIはこのメソッドを使用してNodeJSロガーを作成します。 NodeJSの「プロセス」オブジェクトを最初の引数として指定する必要があります。


## createNodeSystem()

```tsx
createNodeSystem(process: any): CompilerSystem
```

コンパイラーが使用するNodeJSAPIに基づいて「システム」を作成します。 これには、NodeJSを使用したすべてのファイルシステムの読み取りと書き込みが含まれます。 コンパイラ自体は、ノードの `fs`モジュールを認識していません。 他のシステムAPIには、コンテンツをハッシュするための `crypto`の使用が含まれます。 NodeJSの「プロセス」オブジェクトを最初の引数として指定する必要があります。


## parseFlags()

```tsx
parseFlags(args: string[]): ConfigFlags
```

CLIがコマンドライン引数を解析して型付きの `ConfigFlags`オブジェクトにするために使用します。 これは、内部でどのように使用されるかの例です： `parseFlags(process.argv.slice(2))`。


## run()

```tsx
run(init: CliInitOptions): Promise<void>
```

指定されたオプションを使用してCLIを実行します。 これは、Stencilのデフォルトの `bin/stencil`ファイルで使用されますが、外部でも使用できます。


## runTask()

```tsx
runTask(process: any, config: Config, task: TaskCommand,  sys?: CompilerSystem): Promise<void>
```

Runs individual tasks giving a NodeJS `process`, Stencil `config`, and task command. You can optionally pass in the `sys` that's used by the compiler. See [createNodeSystem()](#createnodesystem) for more details.
