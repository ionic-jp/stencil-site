---
title: スクリーンショットのビジュアル差分
description: スクリーンショットのビジュアル差分
url: /docs/screenshot-visual-diff
contributors:
  - adamdbradley
---

# スクリーンショットのビジュアル差分

`EXPERIMENTAL`：スクリーンショットのビジュアル差分テストは現在大規模な開発中であり、安定した状態には達していません。 ただし、支援テストをいただければ幸いです。

## 視覚的回帰テストコマンド

```bash
stencil test --e2e --screenshot
```

## 簡単な例

[Puppeteer](https://github.com/GoogleChrome/puppeteer)は、スクリーンショットを比較するために使用されます。 1つを作成するには、e2eテストを設定する必要があります。例：

```javascript
  it('render something', async () => {
    const page: E2EPage = await newE2EPage();
    await page.setContent('<my-cmp></my-cmp>');
    await page.compareScreenshot('My Component (...is beautiful. Look at it!)', {fullPage: false});
  });

```

## 高度な例

```javascript
describe('stencil-avatar', () => {
  it('renders and responds to the size property', async () => {
    const page = await newE2EPage();

    // In order to test against any global styles you may have, don't forget to set the link to the global css. You don't have to do this if your stencil.config.ts file doesn't build a global css file with globalStyle.
    await page.setContent('<link href="http://localhost:3333/build/stellar-core.css" rel="stylesheet" /><stencil-avatar size="small"></stencil-avatar>');

    const element = await page.find('stencil-avatar');
    expect(element).toHaveClass('hydrated');

    // To start comparing the visual result, you first must run page.compareScreenshot; This will capture a screenshot, and save the file to "/screenshot/images". You'll be able to check that into your repo to provide those results to your team. You can only have one of these commands per test.
    const results = await page.compareScreenshot();

    // Finally, we can test against the previous screenshots.
    // Test against hard pixels
    expect(results).toMatchScreenshot({ allowableMismatchedPixels: 100 })

    // Test against the percentage of changes. if 'allowableMismatchedRatio' is above 20% changed,
    expect(results).toMatchScreenshot({ allowableMismatchedRatio: 0.2 })

  });
});
```


テストを実行した後、プロジェクトの `/screenshot/compare.html`ページを開いて、変更点とその違いを確認できます。

## 現在の問題：

- 本体内のアイテムの内側の幅のみをスクリーンショットします。 現在、スクリーンショットは600x600ピクセルで撮影されているため、「allowableMismatchedRatio」はあまり価値のないオプションになっています。 パペッティアページをレンダリングされたコンテンツの幅にクリップする `await page.readjustSize()`のようなものは、allowedMismatchedRatioをより使いやすくするのに役立ちます。
- さらにテストが必要です！