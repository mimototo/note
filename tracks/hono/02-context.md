# Context と Response

## このレッスンでわかること

- ハンドラの引数 `c` が、1 リクエスト分の作業机であること
- `c.req` で入力を読み、`c.text` / `c.json` / `c.html` で `Response` を返すこと
- 返すものは結局 Web 標準の `Response` であること

## 前提

- [01 なぜ Hono か](./01-why-hono.md)

## 本文

リクエストが来ると、Hono はルートに対応する関数を呼ぶ。その関数が受け取ると `c` が `Context` である。寿命は「このリクエストが始まって、レスポンスを返すまで」。次のリクエストの `c` とは共有されない。

```ts
import { Hono } from 'hono'

const app = new Hono()

app.get('/hello', (c) => {
  const ua = c.req.header('User-Agent')
  return c.text(`hello, ${ua}`)
})

app.get('/api', (c) => {
  return c.json({ message: 'Hello!' })
})

app.post('/posts', (c) => {
  c.status(201)
  return c.text('created')
})
```

よく使う出口:

| メソッド | すること |
| --- | --- |
| `c.text()` | `text/plain` |
| `c.json()` | `application/json` |
| `c.html()` | `text/html` |
| `c.body()` | 本文を自分で。Content-Type も自分で |
| `c.redirect()` | 既定は 302 |
| `c.notFound()` | アプリに設定した Not Found |

`c.body('Thank you', 201, { 'X-Message': 'Hello!' })` は、だいたい次の標準 `Response` と同じである。

```ts
new Response('Thank you', {
  status: 201,
  headers: {
    'X-Message': 'Hello!',
    'Content-Type': 'text/plain',
  },
})
```

Hono を使う理由の一つは、この `Response` を毎回手で組み立てないことと、TypeScript でパスパラメータなどが追えることである。それでも下にあるものは標準 API だ、と覚えておく。ネットワークトラックの HTTP は、まさにこのオブジェクトの中身である。

入力側の入口は `c.req`（`HonoRequest`）。ヘッダ、クエリ、パスパラメータ、本文。パラメータの取り方は次のレッスン。

ハンドラは **Response を return する**。`c.status(201)` だけ呼んで return しない、は完成しない。ミドルウェアで `c.res` を後から触る話は 04 で扱う。

## 確認

- `c` をモジュール先頭のグローバル変数に保存してはいけないのはなぜか
- `c.json({ ok: true })` が返しているものの型（概念）は何か
- ステータス 201 を付けたいとき、どこで指定できるか

## 次へ

- [03 ルーティング](./03-routing.md)

## 公式

- [Context](https://hono.dev/docs/api/context)
- [HonoRequest](https://hono.dev/docs/api/request)
