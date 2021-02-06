---
title: Service Workers
description: Service Workers
url: /docs/service-workers
contributors:
  - jthoms1
  - simonhaenisch
  - DavidFrahm
---

# サービスワーカー

[ServiceWorker](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers)は、[PWAs](https://blog.ionic.io/what-is-a-progressive-web-app/),に不可欠な非常に強力なAPIですが、使いにくい場合があります。 これを支援するために、[Workbox](https://workboxjs.org/).を使用してServiceWorkerのサポートをStencil自体に組み込むことにしました。

## Workboxとは何ですか？

Workboxは、Service WorkerAPIを大幅に簡素化するライブラリです。 これにより、静的アセットのキャッシュを処理したり、ルートを使用してリモートアセットをキャッシュしたり（Expressと同様）、オフラインのGoogleAnalyticsを実行したりできるServiceWorkerをすばやく生成できます。 Workbox上に構築されているため、Workboxが提供する機能を簡単に使用できます。 Workboxの詳細については、[ドキュメントを確認してください](https://developers.google.com/web/tools/workbox/)

## 使用法

Stencilを使用してビルドされたアプリの本番ビルドを実行する場合、Stencilコンパイラは自動的にServiceWorkerを生成し、index.htmlにサービスワーカーを登録するために必要なコードを挿入します。 また、Stencilが生成するファイルはハッシュ化されているため、本番ビルドを実行してアプリに更新をプッシュするたびに、ServiceWorkerは更新を認識し、ユーザーが古いバージョンのサイトにとらわれることがないようにします。

プロジェクトでServiceWorkerを有効にするために必要な手順を実行してみましょう。

- `cd`コマンドでプロジェクトフォルダに移動
- `npm runbuild`を実行します

以上です！ これで、 `www`フォルダーに`sw.js`ファイルがあり、 `www/index.html`ファイルにServiceWorkerを登録するためのコードがあります。

> コンポーネントの収集にはサービスワーカーが必要ないため、コンポーネントスターターではデフォルトでサービスワーカーが有効になっていません。

## 構成

Stencilはその下のWorkboxを使用し、デフォルトでは、 `generateSW`モードを使用して構成オブジェクトからサービスワーカーを生成します。 したがって、すべての[Workbox generateSW config options](https://developers.google.com/web/tools/workbox/modules/workbox-build#full_generatesw_config)をサポートします。 Stencilが使用するデフォルトの構成は次のとおりです。

```tsx
{
  globPatterns: [
    '**/*.{js,css,json,html}'
  ]
};
```

この構成では、アプリのすべてのアセットが事前にキャッシュされます。

この構成を変更するには、StencilConfigの`serviceWorker`パラメーターを使用できます。 次に例を示します。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'www',
      serviceWorker: {
        globPatterns: [
          '**/*.{js,css,json,html,ico,png}'
        ]
      }
    }
  ]
};
```

### サービスワーカーの無効化

ビルド中にServiceWorkerを生成したくない場合は、これをオフにすることができます。 この機能を無効にするには、 `www`出力ターゲットで`serviceWorker`プロパティを `null`に設定します。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'www',
      serviceWorker: null
    }
  ]
};
```

## カスタムサービスワーカーの使用

すでにServiceWorkerがいますか、それともカスタムコードを含めたいですか？ 私たちもそれをサポートします。 ServieWorkerのソースファイルを指定することにより、StencilはWorkboxの「injectManifest」モードに切り替わります。 これにより、ServiceWorerを完全に制御しながら、プリキャッシュマニフェストを自動的に挿入できます。

この機能に必要な手順を実行してみましょう。

- 最初に、CustomServiceWorkerへのパスを `serviceWorker`構成の`swSrc`コマンドに渡す必要があります。 次に例を示します。

```tsx
import { Config } from '@stencil/core';

export const config: Config = {
  outputTargets: [
    {
      type: 'www',
      serviceWorker: {
        swSrc: 'src/sw.js'
      }
    }
  ]
};
```

- 次に、カスタムServiceWorkerに定型コードを含める必要があります。

```tsx
// change to the version you get from `npm ls workbox-build`
importScripts('workbox-v4.3.1/workbox-sw.js');

// your custom service worker code

// the precache manifest will be injected into the following line
self.workbox.precaching.precacheAndRoute([]);
```

このコードは、Workboxライブラリをインポートし、Service Workerの新しいインスタンスを作成し、プリキャッシュ配列を挿入する場所をWorkboxに指示します。

### アップデートが利用可能になったときにリロードトーストを表示する

新しいServiceWorkerが使用可能になると、デフォルトでダウンロードされ、アクティブ化されるのを待機する状態になります。新しいServiceWorkerは、サイトのすべてのタブが閉じられ、サイトに再度アクセスするまで引き継ぎません。これは、キャッシュから提供されるファイルとの競合による予期しない動作を回避するためであり、多くの場合に適切に機能します。

ユーザーに新しい更新にすぐにアクセスするオプションを提供したい場合、一般的な方法は、更新についてユーザーに知らせ、「再読み込み」ボタンを提供するトーストをユーザーに表示することです。リロードにより、新しいServiceWorkerが引き継ぎ、新しいコンテンツを提供し、ページのリロードをトリガーして、キャッシュの問題を回避します。

次の例は、これをIonicフレームワークと組み合わせて示していますが、トースト関連のコードは、どのUIにも簡単に適応できるはずです。以下をルートコンポーネント（通常は `app-root.tsx`）に追加します。

```tsx
@Listen("swUpdate", { target: 'window' })
async onServiceWorkerUpdate() {
  const registration = await navigator.serviceWorker.getRegistration();

  if (!registration?.waiting) {
    // If there is no waiting registration, this is the first service
    // worker being installed.
    return;
  }

  const toast = await toastController.create({
    message: "New version available.",
    buttons: [{ text: 'Reload', role: 'reload' }],
    duration: 0
  });

  await toast.present();

  const { role } = await toast.onWillDismiss();

  if (role === 'reload') {
    registration.waiting.postMessage("skipWaiting");
  }
}
```

`swUpdate`イベントは、新しいServiceWorkerがインストールされるたびにStencilによって発行されます。 ServiceWorkerが登録を待っているときは、トーストが表示されます。 リロードボタンをクリックすると、待機中のServiceWorkerにメッセージが送信され、引き継ぐように通知されます。 このメッセージは、ServiceWorkerが処理する必要があります。 したがって、カスタムのもの（たとえば、 `src/sw.js`）を作成し、`skipWaiting()`を呼び出すリスナーを追加する必要があります。

```tsx
importScripts("workbox-v4.3.1/workbox-sw.js");

self.addEventListener("message", ({ data }) => {
  if (data === "skipWaiting") {
    self.skipWaiting();
  }
});

self.workbox.precaching.precacheAndRoute([]);
```

> StencilConfigで `swSrc`を設定することを忘れないでください。

最後に、新しいService Workerが引き継いだときにアプリをリロードして、古いコードがキャッシュから提供されないようにします。 ルートコンポーネントの `componentWillLoad`ライフサイクルフックにイベントリスナーをアタッチすることで、ServiceWorkerの controllerchange`イベントを使用できます。

```tsx
componentWillLoad() {
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker
      .getRegistration()
      .then(registration => {
        if (registration?.active) {
          navigator.serviceWorker.addEventListener(
            'controllerchange',
            () => window.location.reload()
          );
        }
      })
  }
}
```

### プッシュイベントを処理する

カスタムServiceWorkerの一般的な使用例は、ブラウザーのプッシュ通知を処理することです。 ただし、プッシュ通知を表示できるようになる前に、まずNotifications APIを使用して、ユーザーに許可を要求する必要があります。

```tsx
if ('Notification' in window && 'serviceWorker' in navigator) {
  Notification.requestPermission(status => {
    // status will either be 'default', 'granted' or 'denied'
    console.log(`Notification permissions have been ${status}`);
  });
}
```

現在の許可ステータスは、 `Notification.permission`を使用していつでも確認できます。

許可が与えられた後にユーザーに通知を表示するには、（カスタムServiceWorker内で）ServiceWorkerの登録の `showNotification`メソッドを使用できます。

```tsx
self.registration.showNotification('Hakuna matata.');
```

通常、クライアントにプッシュ通知を送信するバックエンドがあり、ServiceWorkerにそれらを処理してもらいます。 これを行うには、 `push`イベントのイベントリスナーをワーカーに登録します。 イベントのタイプは[`PushEvent`](https://developer.mozilla.org/en-US/docs/Web/API/PushEvent)で、タイプの`data`フィールドは[`PushMessageData`](https://developer.mozilla.org/en-US/docs/Web/API/PushMessageData)。

```tsx
self.addEventListener('push', event => {
  console.log(`Push received with data "${event.data.text()}"`);

  const title = 'Push Notification';
  const options = {
    body: `${event.data.text()}`,
    data: { href: '/users/donald' },
    actions: [
      { action: 'details', title: 'Details' },
      { action: 'dismiss', title: 'Dismiss' },
    ],
  };

  event.waitUntil(self.registration.showNotification(title, options));
});
```

データがJSON文字列の場合、 `data.json()`を使用して、解析されたデータをすぐに取得できます。 `event.waitUntil`メソッドは、非同期の`showNotification`操作が完了する前にServiceWorkerが終了しないようにするために使用されます。

さらに、通知のクリックを処理する必要があります。 APIは、そのためのイベント `notificationclick`と`notificationclose`を提供します。

```tsx
self.addEventListener('notificationclick', event => {
  const notification = event.notification;
  const action = event.action;

  if (action === 'dismiss') {
    notification.close();
  } else {
    // This handles both notification click and 'details' action,
    // because some platforms might not support actions.
    clients.openWindow(notification.data.href);
    notification.close();
  }
});
```

これで、サービスワーカーはプッシュ通知を受信して​​処理できるようになりましたが、クライアントをバックエンドに登録する必要があります。そのため、ブラウザはプッシュサービスを提供し、アプリはこれをサブスクライブできます。サブスクリプションオブジェクトには、クライアントごとに一意の識別子を持つエンドポイントURLが含まれています。サブスクリプションオブジェクトによっても提供される公開鍵で暗号化された通知をそのURLに送信できます。

これを実装するには、最初に各クライアントにブラウザのプッシュサービスをサブスクライブさせてから、サブスクリプションオブジェクトをバックエンドに送信する必要があります。次に、バックエンドはプッシュ通知を生成し、公開鍵で暗号化して、サブスクリプションエンドポイントURLに送信できます。

最初に、ユーザーをプッシュサービスにサブスクライブする関数を実装します。これは、ベストプラクティスとして、プッシュ通知を受信することを通知するユーザーアクションからトリガーする必要があります。通知権限が既に付与されていると仮定すると、以下の機能を使用できます。

```tsx
async function subscribeUser() {
  if ('serviceWorker' in navigator) {
    const registration = await navigator.serviceWorker.ready;

    const subscription = await registration.pushManager
      .subscribe({ userVisibleOnly: true })
      .catch(console.error);

    if (!subscription) {
      return;
    }

    // the subscription object is what we want to send to our backend
    console.log(subscription.endpoint);
  }
}
```

We should also check our subscription every time our app is accessed, because the subscription object can change.

```tsx
self.registration.pushManager.getSubscription().then(subscription => {
  if (!subscription) {
    // ask the user to register for push
    return;
  }

  // update the database
  console.log(subscription);
});
```

### Further Reading

* For more information on push notifications and the related APIs please refer to the [Web Fundamentals Introduction to Push Notifications](https://developers.google.com/web/ilt/pwa/introduction-to-push-notifications) and the [MDN Push API docs](https://developer.mozilla.org/en-US/docs/Web/API/Push_API).
* [This Twitter thread by David Brunelle](https://twitter.com/davidbrunelle/status/1073394572980453376) explains how to implement versioning in your PWA in order to handle breaking API changes. The problem here is that your service worker enabled app will continue to serve an outdated (cached) app against your updated API. In order to solve this a version check can be implemented.


### 参考文献

*プッシュ通知と関連APIの詳細については、[Web Fundamentals Introduction to Push Notifications](https://developers.google.com/web/ilt/pwa/introduction-to-push-notifications)と[MDN Push API docs](https://developer.mozilla.org/en-US/docs/Web/API/Push_API)のドキュメントを参照してください。
* [David BrunelleによるこのTwitterスレッド](https://twitter.com/davidbrunelle/status/1073394572980453376)では、APIの重大な変更を処理するために、PWAにバージョニングを実装する方法について説明しています。 ここでの問題は、Service Worker対応アプリが、更新されたAPIに対して古い（キャッシュされた）アプリを引き続き提供することです。 これを解決するために、バージョンチェックを実装できます。