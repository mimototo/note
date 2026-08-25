# なぜ Hono か

## このレッスンでわかること

- Hono が解いている問題は「速い Express」ではなく「Web Standard の Request/Response を、どの JS ランタイムでも同じコードで扱う」こと
- アプリケーション本体と、ランタイムへの入口が分かれている
- このリポジトリで Hono の次に oRPC を置く理由

## 前提

- [トラック概要](./README.md)
- [フロントエンド地図](../frontend-ecosystem/01-map.md) の「HTTP を扱う層」

## 本文

ブラウザも Cloudflare Workers も Bun も、今はほぼ同じオブジェクトを持っている。

- 入ってくるもの: `Request`
- 返すもの: `Response`
- 外を呼ぶもの: `fetch`

Hono はこの三つを、フレームワークの中心に置いている。Node.js だけを前提に `req` / `res` を扱う Express 型の枠とは、土台が違う。Node で動かすときは [アダプタ](https://github.com/honojs/node-server) が、Node の HTTP と Fetch API のあいだを埋める。

公式の最小例:

```ts
import { Hono } from 'hono'

const app = new Hono()

app.get('/', (c) => c.text('Hono!'))

export default app
```

`export default app` は、Cloudflare Workers や Bun が「このオブジェクトの `fetch` を呼べばよい」と知っている形。ランタイムが変わっても、`app.get` 以下は同じ、というのが売りである。

プロジェクトの足場は公式どおり:

```bash
npm create hono@latest
```

テンプレートで Workers か Bun か Node かを選ぶ。選ぶのは入口とデプロイ手順であって、ルーティングの書き方ではない。

### 何に向いているか（公式の usecase を噛み砕く）

- JSON API
- 既存サーバーの手前のプロキシ
- CDN / Edge で動く小さなアプリ
- ライブラリが内部で持つ HTTP サーバー
- フルスタックの土台（その上に JSX や RPC を載せる）

「React の代わり」ではない。画面が必要なら、別層の UI と組み合わせる。

### oRPC との関係（先回り）

Hono は URL と HTTP メソッドの配線が得意である。oRPC は「関数を型つきで遠隔呼び出し、必要なら OpenAPI も出す」側である。Hono のミドルウェアとして oRPC のハンドラを載せる、という合成が公式にある。だからこのリポジトリでは Hono のあとに oRPC を置く。Hono 内蔵の RPC モード（`hc`）もある。違いは oRPC トラックの 01 で扱う。

## 確認

- 同じ `app` を Workers と Node で動かすとき、変わりやすいのはルート定義か、入口か
- Hono を「フロントエンドフレームワーク」と呼ばない理由は何か
- Express の `req` / `res` と、Hono が中心に置くオブジェクトの違いは何か

## 次へ

- [02 Context と Response](./02-context.md)

## 公式

- [Hono トップ](https://hono.dev/docs/)
- [Web Standards の考え方](https://hono.dev/docs/concepts/web-standard)
- [create-hono](https://hono.dev/docs/getting-started/basic)
