---
title: Stencil Core Dev Server API
description:  Stencil Core Dev Server API
url: /docs/dev-server-api
contributors:
  - adamdbradley
  - dominikpieper
---

# StencilCore開発サーバーAPI

CLI APIは、 `@stencil/core/dev-server`にあります。

## start()

```tsx
start(stencilDevServerConfig: StencilDevServerConfig, logger: Logger, watcher?: CompilerWatcher): Promise<DevServer>
```
