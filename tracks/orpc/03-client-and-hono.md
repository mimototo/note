# Client と Hono

## このレッスンでわかること

- `RPCHandler` が router を HTTP に載せる
- クライアントは router の**型だけ**あればよく、`import type` でサーバー実装をバンドルに入れない
- Hono では Fetch アダプタをミドルウェアとしてマウントする

## 前提

- [02 Procedure と Router](./02-procedure-and-router.md)
- [Hono のミドルウェア](../hono/04-middleware.md)

## 本文

### サーバー（Node の例）

公式 Getting Started では、Node の `http` と `@orpc/server/node` を使う。

```ts
import { createServer } from 'node:http'
import { RPCHandler } from '@orpc/server/node'
import { router } from './router'

const handler = new RPCHandler(router)

const server = createServer(async (req, res) => {
  const { matched } = await handler.handle(req, res, { prefix: '/rpc' })

  if (matched) {
    return
  }

  res.statusCode = 404
  res.end('Not found')
})

server.listen(3000, '127.0.0.1')
```

`prefix: '/rpc'` が URL 上の根。マッチしなければ自分で 404 などへ回す。CORS やログは RPC Handler のプラグイン / interceptor 側。

### クライアント

```ts
import type { RouterClient } from '@orpc/server'
import { createORPCClient } from '@orpc/client'
import { RPCLink } from '@orpc/client/fetch'
import type { router } from './router'

const link = new RPCLink({
  origin: 'http://127.0.0.1:3000',
  url: '/rpc',
})

export const orpc: RouterClient<typeof router> = createORPCClient(link)

const planet = await orpc.planet.find({ id: 1 })
```

`url` はサーバーの `prefix` と揃える。`import type { router }` なら実行コードはクライアントに入らない。同じプロセス（SSR）なら HTTP を飛ばす server-side client もある。導入では Fetch の `RPCLink` だけでよい。

呼び出しはローカルの async 関数に見える。リネームはコンパイルエラーになる、というのが 01 で欲しかった性質である。

### Hono に載せる

Hono は Fetch API の上なので、oRPC も `@orpc/server/fetch` の `RPCHandler` を使う。公式アダプタの基本形:

```ts
import { onError } from '@orpc/server'
import { RPCHandler } from '@orpc/server/fetch'
import { Hono } from 'hono'

const app = new Hono()
const handler = new RPCHandler(router, {
  interceptors: [onError((error) => console.error(error))],
})

app.use('/rpc/*', async (c, next) => {
  const { matched, response } = await handler.handle(c.req.raw, {
    prefix: '/rpc',
    context: {},
  })

  if (matched) {
    return c.newResponse(response.body, response)
  }

  await next()
})

export default app
```

Hono の他ミドルウェアが先に body を読むと、oRPC 側で本文が使えない。公式はそのとき Proxy で Hono のパーサキャッシュへ逃がす方法を書いている。実装が必要になったら [Hono adapter](https://orpc.dev/docs/adapters/hono) を開く。ここでは「Hono の一枚が oRPC」という合成だけ押さえる。

同じ router を REST / OpenAPI としても出せる、と Getting Started は続けている。それは次のレッスン候補である。

## 確認

- クライアントが `import type` するのはなぜか
- `prefix` と `RPCLink` の `url` がずれると何が起きそうか
- Hono の `app.use('/rpc/*', ...)` は、Hono トラックのどの概念か

## 次へ

- oRPC 導入はここまで。HTTP の中身 → [ネットワーク](../networking/)
- 予定（OpenAPI, contract-first）は [トラック README](./README.md)

## 公式

- [Getting Started — Server / Client](https://orpc.dev/docs/getting-started)
- [Hono adapter](https://orpc.dev/docs/adapters/hono)
- [Client-side clients](https://orpc.dev/docs/client/client-side)
