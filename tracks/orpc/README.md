# oRPC

サーバーに普通の TypeScript 関数を書き、クライアントはそれをローカル関数のように呼ぶ。入力は実行時にスキーマで検証し、型は端から端まで推論される。コード生成ステップはない。OpenAPI を第一級で出せるのが、tRPC や Hono RPC と比べたときの大きな軸である。

## ゴール

- REST 手書き / tRPC / Hono の `hc` / oRPC の違いを、層の言葉で言える
- `os` で procedure を作り、ネストした router にまとめられる
- Fetch クライアントで呼び、Hono にハンドラを載せる形が読める

## 前提

- [Hono 01〜04](../hono/) があると、アダプタの意味が取りやすい
- TypeScript の型推論と `import type` がわかる
- Zod などのスキーマを見たことがあると楽（必須ではない）

## レッスン

1. [01 なぜ oRPC か](./01-why-orpc.md) — 型の同期問題と OpenAPI
2. [02 Procedure と Router](./02-procedure-and-router.md) — `os` / input / handler
3. [03 Client と Hono](./03-client-and-hono.md) — 呼ぶ側と載せる側

## 予定（未執筆）

- OpenAPI Handler と仕様の生成
- contract-first（`oc`）
- Middleware / Context / 型付きエラー
- TanStack Query 連携
- SSE とストリーミング

## 公式

- [oRPC](https://orpc.dev/)
- [Getting Started](https://orpc.dev/docs/getting-started)
- [Hono adapter](https://orpc.dev/docs/adapters/hono)
- [Procedure](https://orpc.dev/docs/procedure)
