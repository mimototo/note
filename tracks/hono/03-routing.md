# ルーティング

## このレッスンでわかること

- HTTP メソッドとパスでハンドラが選ばれる
- `:id` のようなパラメータと、ルートのグループ化
- **登録順**が実行順であり、先にマッチしたハンドラで止まる

## 前提

- [02 Context と Response](./02-context.md)

## 本文

```ts
import { Hono } from 'hono'

const app = new Hono()

app.get('/', (c) => c.text('root'))
app.get('/posts/:id', (c) => {
  const id = c.req.param('id')
  return c.json({ id })
})
app.post('/posts', (c) => c.json({ ok: true }, 201))
```

`:id` はパスの可変部分。`c.req.param('id')` で取る。TypeScript では、パス文字列からリテラル型が付くのが Hono の DX の一部である。

### まとめる

関連するルートを別の `Hono` インスタンスに書き、`route` で載せる。

```ts
const posts = new Hono()
posts.get('/', (c) => c.json([]))
posts.get('/:id', (c) => c.json({ id: c.req.param('id') }))

const app = new Hono()
app.route('/posts', posts)
```

`basePath` で接頭辞を固定する方法もある。大きいアプリでは「機能ごとに小さな Hono を合成する」が基本になる。

### 順序

公式が強調している点: ハンドラとミドルウェアは **登録した順** に走る。あるハンドラが Response を返すと、そのリクエストではそこで終わる。

だから:

- 全体に効かせたいミドルウェア（ログなど）は、個別ルートより**上**に書く
- フォールバック（`app.get('*', ...)`）は、具体的なルートより**下**に書く

```ts
app.get('/bar', (c) => c.text('bar'))
app.get('*', (c) => c.text('fallback'))
```

`/bar` を下に書くと、先に `*` が取ってしまうことがある。グループを `route()` するときも、載せる順番を間違えると 404 になる。これはバグというより、マッチの規則である。

ワイルドカードや正規表現など、パターンの詳細は公式の Routing をその都度見る。最初に身体に入れるのは「メソッド + パス + 順番」だけでも足りる。

## 確認

- `GET /posts/42` で `id` を取るコードを、頭の中で書け
- ロガーをルート定義の下に置いたら、何が起きうるか
- 小さな `Hono` を `app.route('/api', api)` したとき、クライアントが見るパスはどうなるか

## 次へ

- [04 ミドルウェア](./04-middleware.md)

## 公式

- [Routing](https://hono.dev/docs/api/routing)
- [Hono クラス](https://hono.dev/docs/api/hono)
