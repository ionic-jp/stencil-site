---
title: 始める
description: Stencilを始める
url: /docs/getting-started
contributors:
  - jthoms1
  - rwaskiewicz
---

# はじめる

## 新しいプロジェクトを開始する

Stencilは最近のLTSバージョンの[NodeJS](https://nodejs.org/)とnpmが必要です。 続ける前にNodeのインストールおよびアップデート済みであることを確認してください。

> npm 6以上を使用する必要があることに注意してください。

```bash
 npm init stencil
```

Stencilを使用して、スタンドアロンなコンポーネントまたはアプリケーション全体を作成できます。 `npm init stencil` を実行すると、
開始するプロジェクトの種類を選択できるプロンプトが表示されます。

```bash
? Pick a starter › - Use arrow-keys. Return to submit.

❯  component     Collection of web components that can be used anywhere
   app           Minimal starter for building a Stencil app or website
```

Selecting the 'component' option will prompt you for the name of your project.

```bash
✔ Pick a starter › component
? Project name › my-first-stencil-project
```

Here, we've named our project 'my-first-stencil project'. After hitting `ENTER` to confirm your choices, the CLI will
scaffold a Stencil project for us in a directory that matches the project name you provided.

Upon successfully creating our project, the CLI will print something similar to the following to the console:
```bash
✔ Project name › my-first-stencil-project
✔ All setup  in 26 ms

  $ npm start
    Starts the development server.

  $ npm run build
    Builds your components/app in production mode.

  $ npm test
    Starts the test runner.


  We suggest that you begin by typing:

   $ cd my-first-stencil-project
   $ npm install
   $ npm start

  Further reading:

   - https://github.com/ionic-team/stencil-component-starter

  Happy coding! 🎈
```

The first section of the output describes a few useful commands available during the development process:

- `npm start` starts a local development server. The development server will open a new browser tab containing your
project's components. The dev-server uses hot-module reloading to update your components in the browser as you modify
them for a rapid feedback cycle.

- `npm run build` creates a production-ready version of your components. The components generated in this step are not
meant to be used in the local development server, but rather within a project that consumes your components.

- `npm test` runs your project's tests. The Stencil CLI has created both end-to-end and unit tests when scaffolding your project.

The second section describes a few commands required to finish getting your project bootstrapped.

> You will need to complete these steps in order to run any of the above commands

```bash
    $ cd my-first-stencil-project
    $ npm install
    $ npm start
```

This will change your current directory to `my-first-stencil-project` (or whatever you named your project), install your
dependencies for you, and start the development server.

At this time, Stencil does not interact with any version control systems (VCS) when running `npm init stencil`. If you
wish to place your project under version control, we recommend initializing your VCS now. If you wish to use
git, run the following after changing your current directory to the root of your Stencil project:

```bash
$ git init
$ git add -A
$ git commit -m "initialize project using stencil cli"
```

## Stencilを更新する

@stencil/coreの最新バージョンを取得するためには、次のコマンドを実行します。

```bash
npm install @stencil/core@latest --save-exact
```
