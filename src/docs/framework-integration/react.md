---
title: ReactとStencilの連携
description: ReactとStencilの連携
url: /docs/react
contributors:
  - jthoms1
  - adamdbradley
  - kensodemann
  - ErikSchierboom
  - brentertz
  - danawoodman
---
# React

`create-react-app`で構築されたアプリケーションにコンポーネントライブラリを含める最も簡単な方法は、`index.js`ファイルから `defineCustomElements()`を呼び出すことです。
EdgeまたはIE11を対象とする場合は、`applyPolyfills`が必要であることに注意してください。

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';

// test-componentはnpmに公開されたWebコンポーネントの名前です。
// npmで公開:
import { applyPolyfills, defineCustomElements } from 'test-components/loader';

ReactDOM.render(<App />, document.getElementById('root'));
registerServiceWorker();

applyPolyfills().then(() => {
  defineCustomElements();
});
```

上記の手順に従うと、ReactでWebコンポーネントを使用できるようになりますが、いくつか考慮が必要な問題があります。https://custom-elements-everywhere.com/ には、現在確認されている問題の概要が記載されています。

## プロパティとイベント

標準のHTML Custom Elementの操作に関してReactが現在抱えている最大の欠点は、非スカラーデータ（文字列や数値ではないデータ）を含むプロパティが適切に渡されず、カスタムイベントが適切に処理されないことです。これらの問題を解決するためには、Custom ElementをReactコンポーネントでラップし、Custom Elementへの`ref`を取得します。そして、`ref`を使用して非スカラープロパティを設定し、`addEventListener`を介してイベントリスナーを追加することです。次は、プロパティの受け渡しでどのように機能するかを示す例です。

```tsx
import React, { useRef, useEffect } from 'react';
import { Forecast } from '../models';
import { iconPaths } from '../util';

const DailyForecast: React.FC<{ forecast: Forecast; scale: string }> = ({ forecast, scale }) => {
  const elementRef = useRef(null);

  useEffect(() => {
    (elementRef.current as any)!.iconPaths = iconPaths;
    (elementRef.current as any)!.forecasts = forecast;
  }, [forecast]);

  return <kws-daily-forecast scale={scale} ref={elementRef}></kws-daily-forecast>;
};

export default DailyForecast;
```

この例では、3つのプロパティがあります。`forecast`はオブジェクトの配列、`iconPaths`はオブジェクト、`scale`は文字列です。`scale`は文字列なので通常通り扱えます。ただし、他の2つのプロパティは非スカラーなので`ref`を介してCustom Elementに設定する必要があります。このようにCustom Elementをラップすると、次のように`DailyForecast`Reactコンポーネントを使用できるため、`kws-daily-forecast`のすべてのインスタンスで`ref`を取得する必要がなくなります。

```tsx
<DailyForecast scale={scale} forecast={f}></DailyForecast>
```

## Bindings

Manually wrapping all Custom Elements in a React Component is a good practice, but it gets tedious quickly. Using Stencil's bindings feature, Stencil-based web components are wrapped in a React component, making them immediately available as React Components.

Stencil's React bindings fix the main issues with React's web component support, including not properly passing properties. Out of the box, React can only pass strings and numbers to components and it cannot listen to custom events. With the bindings, all properties get passed correctly including functions, objects, and arrays. The bindings also account for custom events by creating a prop called ‘on<EventName>’. Finally, types are included, making code more reliable and easier to refactor. These features allow React developers to interact with the web components as though they are React components.

### Getting Started

If you're going to compile your Stencil components into multiple framework libraries, it's recommended to create a monorepo project that contains the Stencil library alongside each framework library for easier maintainability. For a complete reference project, see [stencil-ds-plugins-demo](https://github.com/ionic-team/stencil-ds-plugins-demo).

In this example, `component-library` is a Stencil library and `component-library-react` is the React library where Stencil-based React components will be generated.

Create a monorepo directory then move any existing Stencil component repos into the monorepo project:

```bash
mkdir component-mono
mv component-library component-mono/component-library
```

### React Component Library Setup

First, we need to set up the React library that will contains the Stencil-generated React components. You can create your own React project structure or use the [Stencil React template repo](https://github.com/ionic-team/stencil-ds-react-template) to bootstrap it. It's recommended that this repo lives as a sibling to your Stencil component library, so within the Stencil monorepo, clone the project:

```bash
git clone https://github.com/ionic-team/stencil-ds-react-template
```

In `package.json`, if you already have a published Stencil library on npm, change the `component-library` dependency name to your library name then run `npm install`.

If no library has been published on npm and/or you'd like to build and test locally, remove the `component-library` dependency. Next, change into the Stencil library directory and run `npm link`. Change back into the React library and run `npm link <library>` where `library` is your Stencil library name. Finally, run `npm install`.

### Stencil Config setup

With a basic React library configured, the next step is configuring Stencil to output React components. Change into your Stencil component library directory then install the React output target:

```bash
npm install @stencil/react-output-target --save-dev
```

Next, open `stencil.config.ts` then add React to the output target list:

```tsx
import { Config } from '@stencil/core';
import { reactOutputTarget } from '@stencil/react-output-target';

export const config: Config = {
  namespace: 'demo',
  outputTargets: [
    reactOutputTarget({
      componentCorePackage: 'component-library',
      proxiesFile: '../component-library-react/src/components.ts',
      includeDefineCustomElements: true,
    }),
    {
      type: 'dist',
    },
  ],
};
```

#### componentCorePackage

This is the package name of your core Stencil library containing just your web components that gets published on npm. This package is referenced as a dependency by the React package. For example, Ionic Framework's core library is `@ionic/core` and is a dependency of `@ionic/react`.

#### proxiesFile

This is the output file that gets generated by the outputTarget. This file should reference a different package location. In the monorepo example here, we are choosing a sibling directory’s src directory - stencil-ds-react-template - with package name `component-library-react`. During a Stencil build, a React package is created that exports all components defined in this file.

#### includeDefineCustomElements

Specifying true here (recommended) means the consuming React application doesn't have to manually import and call `defineCustomElements()` in `index.js`.


With React support configured, run `npm run build` to create the Stencil React bindings. You'll see the newly generated files in `component-library-react`'s dist folder.

Next, change directory into `stencil-ds-react-template` then install dependencies and build the project:

```bash
npm install
npm run build
```

Next, publish the React library on npm if desired then add it as a project dependency.

If you don't want to publish the library on npm and/or you'd like to build and test it locally in a React app, link the project with `npm link` then change directory into your React app and run `npm link component-library-react`.

### Usage

Since the Stencil-generated React library is effectively a regular React library, use it as you would any React library. Import components from the package:

```tsx
import { DemoComponent } from 'component-library-react';
```
