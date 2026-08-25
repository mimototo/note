# ミドルウェア

## このレッスンでわかること

- ハンドラは Response を返す。ミドルウェアは `next` の前後で Request / Response に介入する
- 構造は onion（内側のハンドラを、外側が包む）
- `c.set` / `c.get` で、同じリクエストのあいだだけ値を渡す

## 前提

- [03 ルーティング](./03-routing.md)

## 本文

公式の定義をそのまま使う。

- **Handler** — `Response` を返す。一つのリクエストで本命のハンドラは一つ
- **Middleware** — `await next()` して何も返さない（後続に進む）か、自分で `Response` を返して打ち切る

イメージはタマネギである。リクエストは外から内へ、レスポンスは内から外へ戻る。

```ts
import { Hono } from 'hono'
import { etag } from 'hono/etag'
import { logger } from 'hono/logger'

const app = new Hono()
app.use(etag(), logger())
```

自前の例（レスポンス時間）:

```ts
app.use(async (c, next) => {
  const start = performance.now()
  await next()
  const end = performance.now()
  c.res.headers.set('X-Response-Time', `${end - start}`)
})
```

`await next()` の**前**が「ハンドラに入る前」、**後**が「ハンドラが Response を作ったあと」。ヘッダを足す、ログを出す、認証で弾く、がこの二つに分かれる。認証で弾くなら `next` を呼ばず 401 の Response を返す。

### リクエスト限定の値

```ts
const app = new Hono<{ Variables: { message: string } }>()

app.use(async (c, next) => {
  c.set('message', 'Hono is cool!!')
  await next()
})

app.get('/', (c) => {
  const message = c.get('message')
  return c.text(message)
})
```

`Variables` をジェネリクスで渡すと `c.get` に型が付く。値は**このリクエスト限り**。次のリクエストには残らない。グローバル状態ではない。

再利用するミドルウェアは `createMiddleware`（`hono/factory`）に切り出すと、`c` と `next` の型が壊れにくい。`.use()` をチェーンすると、Hono は後続ハンドラへ `Variables` の型を蓄積する。

組み込みは CORS、JWT、secure headers、compress など多い。全部を暗記しない。必要になったら [builtin middleware](https://hono.dev/docs/middleware/builtin/cors) を見る。

oRPC を載せるときも、結局はこの `app.use('/rpc/*', ...)` の形になる。ミドルウェアが本文を先に読んでしまうと body が二重消費される、という注意は oRPC 側の Hono アダプタに書いてある。層としては「Hono の onion の一枚が oRPC」である。

## 確認

- `await next()` を呼ばないミドルウェアは、後続のルートハンドラを実行するか
- `X-Response-Time` を測る処理は、なぜ `next` のあとでヘッダを付けるのか
- `c.set` した値を、別ユーザーの次のリクエストで読めるか

## 次へ

- Hono 導入はここまで。型つき API へ → [oRPC](../orpc/)
- HTTP そのものを分解する → [ネットワーク](../networking/)
- 予定レッスン（Validator, `hc`）は [トラック README](./README.md)

## 公式

- [Middleware の概念](https://hono.dev/docs/concepts/middleware)
- [Middleware ガイド](https://hono.dev/docs/guides/middleware/)
- [factory / createMiddleware](https://hono.dev/docs/helpers/factory)
