# なぜ oRPC か

## このレッスンでわかること

- 手書き REST で型がずれる問題を、oRPC がどこで消しているか
- tRPC・Hono RPC（`hc`）との違いの軸（OpenAPI、スキーマ、ランタイム）
- 「HTTP がなくなる」わけではなく、HTTP の上に関数の契約を載せること

## 前提

- [トラック概要](./README.md)
- [Hono が HTTP の配線であること](../hono/01-why-hono.md)

## 本文

古典的な API 作業は三つに分裂しやすい。

1. サーバー: `POST /planets` の実装
2. クライアント: `fetch('/planets')` と手書きの型
3. 外部向け: OpenAPI YAML を別ファイルでメンテ

どれか一つを変えて残りを忘れる、が日常である。oRPC の主張は、**サーバーに書いた関数の型がクライアントに流れ、実行時にはスキーマが入力を検証し、必要なら同じ定義から OpenAPI も出る**、である。生成コマンドでクライアント SDK を吐き続ける、が必須ではない。

Getting Started の経路は短い。

1. procedure を書いて router にまとめる
2. HTTP で serve する
3. 型付きクライアントから呼ぶ

インストール例（公式 Getting Started。v2 は beta 指定があるので、作業時点の公式を優先する）:

```bash
npm install @orpc/server@beta @orpc/client@beta zod
```

Zod 以外に Valibot や ArkType など、[Standard Schema](https://standardschema.dev/) 互換ならよい。

### 近くの選択肢

| | 向いている感覚 | 足りにくいところ |
| --- | --- | --- |
| 手書き REST + OpenAPI | 言語を問う公開 API | 型と YAML の三重管理 |
| tRPC | TS 同士の端から端 | 公開 REST / OpenAPI が本筋ではない |
| Hono RPC + `hc` | すでに Hono のルートがある | OpenAPI 第一級ではない。ルート設計が HTTP のまま |
| oRPC | TS の DX と OpenAPI の両方 | HTTP そのものの学習にはならない。下は Hono 等が必要 |

Hono の RPC モードは「Hono のルートと Validator からクライアント型を取る」。oRPC は「procedure が先で、HTTP への載せ方はアダプタ」。どちらも「型を手で二重に書かない」。公開仕様や契約先行まで視野に入れるなら oRPC の公式ストーリーに近い。

### 消えないもの

ブラウザは今も HTTP で JSON を運ぶ。DNS も TCP も関係する。oRPC はネットワークを置換しない。置換するのは、クライアントが `fetch` の URL と型を手で合わせる作業である。

## 確認

- サーバーの関数名を変えたとき、oRPC のクライアントで起きてほしいことは何か
- Hono だけで API を書いてはいけない、わけではない。oRPC を足す理由を一つ言え
- OpenAPI が欲しい相手は、TypeScript クライアントだけか

## 次へ

- [02 Procedure と Router](./02-procedure-and-router.md)

## 公式

- [Getting Started](https://orpc.dev/docs/getting-started)
- [oRPC トップ（機能一覧）](https://orpc.dev/)
- [Hono RPC](https://hono.dev/docs/guides/rpc)（対比用）
