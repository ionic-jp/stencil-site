---
title: テストのコンフィグ
description: テストのコンフィグ
url: /docs/testing-config
contributors:
  - adamdbradley
  - mattcosta7
  - viernullvier
---

# 構成のテスト

`testing`構成設定は、テストで使用する必要があるjest構成に対応するオブジェクトを指定します。 ステンシルはデフォルトの構成を提供します。これはおそらく編集する必要はありませんが、Jestと同じ構成オプションで拡張できます。 構成の詳細については、[構成Jestガイド](https://jestjs.io/docs/en/configuration.html)を参照してください。

> Jestを構成する通常の方法（`package.json`および`jest.config.js`）は、 `stenciltesting`コマンドでは使用されないことに注意してください。 Jestは引き続き使用できますが、プリセットの構成、トランスパイル、および正しいコマンドの設定は、プロジェクトで行う必要があります。

e2eテストを構成するために、いくつかの追加のステンシル固有のオプションもここで設定できます。

```tsx
export interface TestingConfig extends JestConfig {
  /**
   * The `allowableMismatchedPixels` value is used to determine an acceptable
   * number of pixels that can be mismatched before the image is considered
   * to have changes. Realistically, two screenshots representing the same
   * content may have a small number of pixels that are not identical due to
   * anti-aliasing, which is perfectly normal. If the `allowableMismatchedRatio`
   * is provided it will take precedence, otherwise `allowableMismatchedPixels`
   * will be used.
   */
  allowableMismatchedPixels?: number;

  /**
   * The `allowableMismatchedRatio` ranges from `0` to `1` and is used to
   * determine an acceptable ratio of pixels that can be mismatched before
   * the image is considered to have changes. Realistically, two screenshots
   * representing the same content may have a small number of pixels that
   * are not identical due to anti-aliasing, which is perfectly normal. The
   * `allowableMismatchedRatio` is the number of pixels that were mismatched,
   * divided by the total number of pixels in the screenshot. For example,
   * a ratio value of `0.06` means 6% of the pixels can be mismatched before
   * the image is considered to have changes. If the `allowableMismatchedRatio`
   * is provided it will take precedence, otherwise `allowableMismatchedPixels`
   * will be used.
   */
  allowableMismatchedRatio?: number;

  /**
   * Matching threshold while comparing two screenshots. Value ranges from `0` to `1`.
   * Smaller values make the comparison more sensitive. The `pixelmatchThreshold`
   * value helps to ignore anti-aliasing. Default: `0.1`
   */
  pixelmatchThreshold?: number;

  /**
   * Additional arguments to pass to the browser instance.
   */
  browserArgs?: string[];

  /**
   * Path to a Chromium or Chrome executable to run instead of the bundled Chromium.
   */
  browserExecutablePath?: string;

  /**
   * Whether to run browser e2e tests in headless mode. Defaults to true.
   */
  browserHeadless?: boolean;

  /**
   * Slows down e2e browser operations by the specified amount of milliseconds.
   * Useful so that you can see what is going on.
   */
  browserSlowMo?: number;

  /**
   * Array of browser emulations to be using during e2e tests. A full e2e
   * test is ran for each emulation.
   */
  emulate?: EmulateConfig[];

  /**
   * Path to the Screenshot Connector module.
   */
  screenshotConnector?: string;
}

export interface EmulateConfig {
  /**
   * Predefined device descriptor name, such as "iPhone X" or "Nexus 10".
   * For a complete list please see: https://github.com/puppeteer/puppeteer/blob/main/src/DeviceDescriptors.ts
   */
  device?: string;

  /**
   * User-Agent to be used. Defaults to the user-agent of the installed Puppeteer version.
   */
  userAgent?: string;

  viewport?: EmulateViewport;
}


export interface EmulateViewport {

  /**
   * Page width in pixels.
   */
  width: number;

  /**
   * page height in pixels.
   */
  height: number;

  /**
   * Specify device scale factor (can be thought of as dpr). Defaults to 1.
   */
  deviceScaleFactor?: number;

  /**
   * Whether the meta viewport tag is taken into account. Defaults to false.
   */
  isMobile?: boolean;

  /**
   * Specifies if viewport supports touch events. Defaults to false
   */
  hasTouch?: boolean;

  /**
   * Specifies if viewport is in landscape mode. Defaults to false.
   */
  isLandscape?: boolean;

}
```
