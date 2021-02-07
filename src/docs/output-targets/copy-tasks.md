---
title: Stencilコピータスク
description: Stencilコピータスク
url: /docs/copy-tasks
contributors:
  - adamdbradley
  - manucorporat
  - jeanbenitez
---


# 出力ターゲットのコピータスク

各出力ターゲットは、独自の `copy`構成を持つことができます。これは、出力ターゲットのビルドディレクトリにコピーする必要のあるファイルまたはフォルダを定義するオブジェクトの配列です。

### src

配列内の各オブジェクトには、絶対パス、`srcDir`からの相対パス、またはglobパターンのいずれかである`src`プロパティが含まれている必要があります。 デフォルトでは、宛先にコピーされたアイテムはソースと同じ名前になります。

以下の `www`出力ターゲットの例内の`copy`構成では、ビルドはディレクトリ全体を `src/images`から`www/images`にコピーします。 この例では、 `srcDir`プロパティが設定されていないため、デフォルトのソースディレクトリは`src`です。

```tsx
  outputTargets: [
    {
      type: 'www',
      copy: [
        { src: 'images' }
      ]
    }
  ]
```


### dest

構成では、オプションの `dest`プロパティを提供することもできます。これは、絶対パス、またはその出力ターゲットのビルドディレクトリからの相対パスのいずれかです。 以下の例では、ビルドディレクトリをデフォルトではなく `public`にカスタマイズしました。これにより、`src/files/fonts`が `public/static/web-fonts`にコピーされます。

```tsx
  outputTargets: [
    {
      type: 'www',
      dir: 'public',
      copy: [
        { src: 'files/fonts', dest: 'static/web-fonts' }
      ]
    }
  ]
```

### warn

デフォルトでは、ファイルまたはディレクトリが使用できない場合、コピータスクがそれを見つけられなくても警告は表示されません。 コピータスクのソースが見つからない場合の警告を表示するには、copy configオブジェクトで `warn:true`を設定してください。

```tsx
  outputTargets: [
    {
      type: 'dist',
      copy: [
        { src: 'fonts', warn: true }
      ]
    }
  ]
```
