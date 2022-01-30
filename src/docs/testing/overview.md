---
title: テスト
description: テストの概要
url: /docs/testing-overview
contributors:
  - adamdbradley
  - brandyscarney
  - camwiegert
  - kensodemann
  - rwaskiewicz
---

# テスト

In order to ensure that your Stencil components work the way you expect, Stencil provides testing support out of the
box. Stencil offers both unit testing and end-to-end testing capabilities.

## ユニットテストとエンドツーエンドテスト

Testing within Stencil is broken up into two distinct types: Unit tests and End-to-end (e2e) tests.

There are several philosophies on how testing should be done, and how to differentiate what should be considered a unit
test versus an end-to-end test. Stencil takes an opinionated stance so developers have a description of each to better
choose when to use each type of testing:

**Unit tests** focus on testing a component's methods in isolation. For example, when a method is given the argument
`X`, it should return `Y`.

**End-to-end tests** focus on how the components are rendered in the DOM and how the individual components work
together. For example, when `my-component` has the `X` attribute, the child component then renders the text `Y`, and
expects to receive the event `Z`.

Both testng types use [Jest](https://jestjs.io/) as the JavaScript testing solution. End-to-end tests also use
[Puppeteer](https://pptr.dev/) instead of a Node environment. This allows end-to-end tests to run within an actual
browser in order to provide more realistic results.

## Library Support

Stencil uses [Jest](https://jestjs.io/) and [Puppeteer](https://pptr.dev/) as its testing libraries, and allows
developers to install both libraries using their preferred package manager. Stencil currently supports:
- Jest v24.9.0 through v27.Y.Z (inclusive)
- Puppeteer v1.19.0 through v10.Y.Z (inclusive)

If you created a project using `npm init stencil`, these libraries were installed for you. Depending on when your
project was created, you may or may not have the latest supported version installed.

## テストコマンド

Stencil tests are run using the command `stencil test`, followed by one or more optional flags:
- `--spec` to run unit tests
- `--e2e` to run end-to-end tests
- `--watchAll` to watch the file system for changes, and rerun tests when changes are detected

When the `--spec` and/or `--e2e` flags are provided, Stencil will automatically run the tests associated with each flag.

Below a series of example `npm` scripts which can be added to the project's `package.json` file to run Stencil tests:

```json
{
    "scripts": {
      "test": "stencil test --spec",
      "test.watch": "stencil test --spec --watchAll",
      "test.end-to-end": "stencil test --e2e"
    }
}
```

Each command above begins with `stencil test`, which tells Stencil to run tests. Note that each `stencil test` command
in example above is followed one or more of the optional flags. Looking at each script, one at a time:
- the `test` script runs unit tests for our Stencil project.
- the `test.watch` script runs unit tests for our Stencil project. It watches the filesystem for changes, and reruns
tests when changes are detected.
- the `test.end-to-end` script runs the end-to-end tests for our Stencil project.

If you created a project using `npm init stencil`, these scripts are provided to you automatically.

Stencil does not prescribe any specific naming convention for the names of your scripts. The `test.watch` script could
as easily be named `test-watch`, `test.incremental`, etc. As long as the script itself uses the `stencil test` command,
your tests should be run.

### 構成のテスト

ステンシルは、すでに収集したデータからデフォルトを適用します。 たとえば、ステンシルは、どのディレクトリを調べるか、どのファイルがspecファイルおよびe2eファイルであるかをすでに認識しています。 Jestは、同じ構成名を使用して構成できますが、ステンシル構成の `testing`プロパティを使用するようになりました。 詳細については、[テスト構成ドキュメント](/docs/config/tests)を参照してください。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  testing: {
    testPathIgnorePatterns: [...]
  }
};
```

## VSCodeでのテストの実行とデバッグ

次の構成を `.vscode/launch.json`に追加すると、VS Code Debuggerを使用して、エディターで現在アクティブなファイルのステンシルテストランナーを実行できます。 実行するテストファイルが表示されていることを確認してから、デバッグ構成をそれぞれ選択し（スペックテストかe2eテストかに応じて）、再生ボタンを押します。

```json
{
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "E2E Test Current File",
      "cwd": "${workspaceFolder}",
      "program": "${workspaceFolder}/node_modules/.bin/stencil",
      "args": ["test", "--e2e", "${relativeFile}"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen",
      "disableOptimisticBPs": true
    },
    {
      "type": "node",
      "request": "launch",
      "name": "Spec Test Current File",
      "cwd": "${workspaceFolder}",
      "program": "${workspaceFolder}/node_modules/.bin/stencil",
      "args": ["test", "--spec", "${relativeFile}"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen",
      "disableOptimisticBPs": true
    }
  ]
}
```

## その他の資料

- [StencilJSでのユニットテストの基本](https://www.joshmorony.com/the-basics-of-unit-testing-in-stencil-js/)
