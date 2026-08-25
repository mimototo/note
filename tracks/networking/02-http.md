# HTTP を分解する

## このレッスンでわかること

- リクエストとレスポンスが、開始行・ヘッダ・ボディでできている
- メソッドとステータスは「何をしたいか」「どう終わったか」
- Hono の `c.req` / `c.json` / `c.status` が、どの部品の糖衣か

## 前提

- [01 層で見る通信](./01-layers.md)
- あるとよい: [Hono Context](../hono/02-context.md)

## 本文

HTTP メッセージはテキスト（と、そのあとに続くバイト列）である。概念だけ書く。

リクエスト:

```text
GET /rpc HTTP/1.1
Host: 127.0.0.1:3000
Content-Type: application/json

{"id":1}
```

レスポンス:

```text
HTTP/1.1 200 OK
Content-Type: application/json

{"id":1,"name":"Earth"}
```

部品:

- **開始行** — リクエストならメソッドとパス（と版）。レスポンスならステータス
- **ヘッダ** — メタデータ。`Host`、`Content-Type`、`Authorization`、CORS 関連など
- **ボディ** — なくてもよい。JSON やファイルがここ

### メソッド

よく使うものだけ。

- `GET` — 取得。ボディを付けないのが慣例
- `POST` — 作る / 送る
- `PUT` / `PATCH` — 置き換え / 部分更新
- `DELETE` — 削除

Hono は `app.get` / `app.post` がこれ。oRPC の RPC モードは、クライアントからは関数に見えるが、運んでいるのは結局 HTTP メッセージである（パスは prefix 配下の RPC 用）。

### ステータス

- 2xx 成功（`201` 作成など）
- 3xx 別の場所へ
- 4xx クライアント側の問題（`404` そのパスはない、`401` 認証、`400` 入力）
- 5xx サーバー側の失敗

Hono の `c.status(201)` や `c.json(data, 201)` がここ。oRPC の入力スキーマ違反は、ハンドラに入る前に 4xx 系として返る、と考えてよい（正確なコードは公式のエラーのページ）。

### Content-Type

ボディの解釈契約。`application/json` なら JSON として読む。Hono の `c.json()` はヘッダとシリアライズをまとめてやる。`c.text()` は `text/plain`。

### このリポジトリでの対応表

| HTTP | Hono | oRPC（導入の範囲） |
| --- | --- | --- |
| パス | `app.get('/posts/:id')` など | 多くは `/rpc` 以下に畳まれる |
| メソッド | `app.get` / `app.post` | RPC として隠れることが多い |
| ヘッダ | `c.req.header` / `c.header` | Link や context で付与 |
| ボディ | `c.req.json()` / `c.json()` | procedure の input / return |
| ステータス | `c.status` | エラー型とハンドラに委譲 |

「oRPC を使うから HTTP を知らなくてよい」にはならない。デバッグのときは必ずこの部品に戻る。

## 確認

- `c.json({ ok: true })` がセットするヘッダは何か
- `404` は DNS 失敗か、HTTP のステータスか
- GET のルートに JSON ボディを期待する設計が、なぜ気持ち悪いか（慣例の話でよい）

## 次へ

- 同じ HTTP を Go で → [Go](../go/)
- ポートを箱に出す → [Docker](../docker/)

## 公式

- [MDN: HTTP の概要](https://developer.mozilla.org/ja/docs/Web/HTTP/Guides/Overview)
- [MDN: ステータスコード](https://developer.mozilla.org/ja/docs/Web/HTTP/Reference/Status)
- [MDN: メソッド](https://developer.mozilla.org/ja/docs/Web/HTTP/Reference/Methods)
