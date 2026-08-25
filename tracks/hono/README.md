# Hono

Web Standards（`Request` / `Response` / `fetch`）の上に乗った、小さい Web フレームワーク。同じアプリケーションコードを Cloudflare Workers、Bun、Deno、Node.js などで動かす、というのが設計の中心にある。

## ゴール

- Hono が Express 互換の「全部入りサーバー」ではなく、Fetch API の薄い層だと説明できる
- `Context` からリクエストを読み、`c.json` などで Response を返せる
- ルートの合成とミドルウェアの順序（onion）を読める
- 次の oRPC を載せる場所（HTTP ハンドラ）がわかる

## 前提

- [フロントエンドの地図](../frontend-ecosystem/01-map.md) を読んでいると楽
- TypeScript の関数とオブジェクトが読める
- HTTP のメソッドとパスの意味（詳細は [networking](../networking/)）

## レッスン

1. [01 なぜ Hono か](./01-why-hono.md) — Web Standards とマルチランタイム
2. [02 Context と Response](./02-context.md) — 1 リクエストの作業机
3. [03 ルーティング](./03-routing.md) — パス、パラメータ、グループ、順序
4. [04 ミドルウェア](./04-middleware.md) — onion と `c.set`

## 予定（未執筆）

- Validator（Zod など）と型
- RPC モードと `hc` クライアント（oRPC との違いもここで整理する）
- JSX / html helper
- ランタイム別アダプタ（Node の `@hono/node-server` など）
- 大きいアプリでの `route()` と型のチェーン

## 公式

- [Hono ドキュメント](https://hono.dev/docs/)
- [Getting Started](https://hono.dev/docs/getting-started/basic)
- [Routing](https://hono.dev/docs/api/routing)
- [Context](https://hono.dev/docs/api/context)
- [Middleware](https://hono.dev/docs/concepts/middleware)
