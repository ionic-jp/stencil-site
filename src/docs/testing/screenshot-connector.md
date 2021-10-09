---
title: スクリーンショットコネクター
description: スクリーンショットコネクター
url: /docs/screenshot-connector
contributors:
  - SheepFromHeaven
---

# スクリーンショットコネクタ

スクリーンショットテストプロセスで使用されるスクリーンショットコネクタモジュールを構成して、テストのキャッシュ、比較、公開のデフォルトの動作を変更できます。
コネクタクラスを定義するファイルを作成し、ステンシルテスト構成でそれを指すだけです。

```tsx
export const config: Config = {
  ...
  testing: {
    screenshotConnector: './connector.js'
  }
};
```

## Writing a connector
To write a connector, import the base `ScreenshotConnector` class from stencil and extend it:

##コネクタの書き込み
コネクタを作成するには、基本の `ScreenshotConnector`クラスをステンシルからインポートして拡張します。

```javascript
const { ScreenshotConnector } = require('@stencil/core/screenshot');

module.exports = class ScreenshotCustomConnector extends ScreenshotConnector {
  ...
};
```

> これを行う方法の良いリファレンスについては、デフォルトの [`StencilLocalConnector`](https://github.com/ionic-team/stencil/blob/main/src/screenshot/connector-local.ts) をご覧ください。

## メソッド
ステンシルからインポートおよび拡張できるベースコネクタには、上書き可能な次のメソッドがあります。

```tsx
export interface ScreenshotConnector {
  initBuild(opts: ScreenshotConnectorOptions): Promise<void>;

  pullMasterBuild(): Promise<void>;

  getMasterBuild(): Promise<ScreenshotBuild>;

  getScreenshotCache(): Promise<ScreenshotCache>;

  completeBuild(masterBuild: ScreenshotBuild): Promise<ScreenshotBuildResults>;

  publishBuild(buildResults: ScreenshotBuildResults): Promise<ScreenshotBuildResults>;

  updateScreenshotCache(screenshotCache: ScreenshotCache, buildResults: ScreenshotBuildResults): Promise<ScreenshotCache>;

  generateJsonpDataUris(build: ScreenshotBuild): Promise<void>;
}
```
インターフェイスへの参照については、[ここを参照](#interfaces)

### initBuild（options）
このメソッドは、コネクタをセットアップし、テストを実行するためのすべての準備をするために呼び出されています。スクリーンショットテストの実行に必要な変数、ファイルパス、およびフォルダー構造の設定を担当します。

>自分が何をしているのかがわかっている場合にのみ、このメソッドを上書きしてください。簡単に拡張できるように、必ず `super.initBuild`を呼び出してください。

### pullMasterBuild()
コネクタを初期化し、ビルドを設定した後、このメソッドを実行して、マスタービルドをプルできるようにします。これは、スクリーンショットがテストが実行されているマシン以外の場所に保存されている場合に非常に役立ちます。

### getMasterBuild()
テストがセットアップされたので、このメソッドを実行する準備ができて、マスタービルドを返すために呼び出されています。したがって、マスタービルドをファイルからロードする代わりに、APIからフェッチして、このメソッドで返すことができます。

### getScreenshotCache()
このメソッドは、現在のビルド結果で拡張されるスクリーンショットキャッシュを返すために呼び出されています。

### completeBuild(masterBuild)
テストを実行し、構成されたフォルダーにスクリーンショットを生成した後、このメソッドが呼び出されて、結果のjsonデータが作成されます。この時点で、イメージはそこにあり、マスタービルドはオプションとして渡されています。

>自分が何をしているのかがわかっている場合にのみ、このメソッドを上書きしてください。簡単に拡張できるように、必ず `super.completeBuild`を呼び出してください。

### publishBuild(buildResults)
ビルドが完了し、結果が生成されたので、このメソッドは結果データとともに呼び出されます。ここでは、結果をjsonファイルに書き込んだり、リモートの場所に送信したりできます。デフォルトの `StencilLocalConnector`では、このメソッドは比較アプリのhtmlを作成します。

### updateScreenshotCache(screenshotCache,buildResults)
実行全体の最後に、スクリーンショットキャッシュをこのメソッドで更新する必要があります。したがって、ここからファイルに書き込んだり、APIに送信したりできます。


## インターフェース
```tsx
export interface ScreenshotConnectorOptions {
  buildId: string;

  buildMessage: string;

  buildAuthor?: string;

  buildUrl?: string;

  previewUrl?: string;

  appNamespace: string;

  buildTimestamp: number;

  logger: Logger;

  rootDir: string;

  cacheDir: string;

  packageDir: string;

  screenshotDirName?: string;

  imagesDirName?: string;

  buildsDirName?: string;

  currentBuildDir?: string;

  updateMaster?: boolean;

  allowableMismatchedPixels?: number;

  allowableMismatchedRatio?: number;

  pixelmatchThreshold?: number;

  waitBeforeScreenshot?: number;

  pixelmatchModulePath?: string;
}

export interface ScreenshotBuild {
  id: string;

  message: string;

  author?: string;

  url?: string;

  previewUrl?: string;

  appNamespace: string;

  timestamp: number;

  screenshots: Screenshot[];
}

export interface ScreenshotBuildResults {
  appNamespace: string;

  masterBuild: ScreenshotBuild;

  currentBuild: ScreenshotBuild;

  compare: ScreenshotCompareResults;
}

export interface ScreenshotCompareResults {
  id: string;

  a: {
    id: string;
    message: string;
    author: string;
    url: string;
    previewUrl: string;
  };

  b: {
    id: string;
    message: string;
    author: string;
    url: string;
    previewUrl: string;
  };

  timestamp: number;

  url: string;

  appNamespace: string;

  diffs: ScreenshotDiff[];
}

export interface ScreenshotCache {
  timestamp?: number;

  lastBuildId?: string;

  size?: number;

  items?: {
    /**
     * Cache key
     */
    key: string;

    /**
     * Timestamp used to remove the oldest data
     */
    ts: number;

    /**
     * Mismatched pixels
     */
    mp: number;
  }[];
}

export interface Screenshot {
  id: string;

  desc?: string;

  image: string;

  device?: string;

  userAgent?: string;

  width?: number;

  height?: number;

  deviceScaleFactor?: number;

  hasTouch?: boolean;

  isLandscape?: boolean;

  isMobile?: boolean;

  testPath?: string;

  diff?: ScreenshotDiff;
}

export interface ScreenshotDiff {
  mismatchedPixels: number;

  id?: string;

  desc?: string;

  imageA?: string;

  imageB?: string;

  device?: string;

  userAgent?: string;

  width?: number;

  height?: number;

  deviceScaleFactor?: number;

  hasTouch?: boolean;

  isLandscape?: boolean;

  isMobile?: boolean;

  allowableMismatchedPixels: number;

  allowableMismatchedRatio: number;

  testPath?: string;

  cacheKey?: string;
}
```
