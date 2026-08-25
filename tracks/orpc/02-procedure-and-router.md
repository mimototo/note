# Procedure と Router

## このレッスンでわかること

- procedure は「遠隔から呼べる関数」で、`os` ビルダーで作る
- `.input(schema)` が実行時バリデーションと、ハンドラ内の `input` の型になる
- router はただのオブジェクト。ネストしたキーが呼び出しパスになる

## 前提

- [01 なぜ oRPC か](./01-why-orpc.md)

## 本文

公式 Getting Started の骨格を、読み用に抜粋する（コピーして動かす前に、今の公式を開くこと）。

```ts
import { os } from '@orpc/server'
import * as z from 'zod'

export const listPlanets = os
  .handler(async () => {
    return [
      { id: 1, name: 'Earth' },
      { id: 2, name: 'Mars' },
    ]
  })

export const findPlanet = os
  .input(z.object({ id: z.number() }))
  .handler(async ({ input }) => {
    return { id: input.id, name: 'Earth' }
  })

export const createPlanet = os
  .input(z.object({ name: z.string(), description: z.string().optional() }))
  .handler(async ({ input }) => {
    return { id: 3, ...input }
  })

export const router = {
  planet: {
    list: listPlanets,
    find: findPlanet,
    create: createPlanet,
  },
}
```

要点は公式が先に書いている。

- `.input` がある呼び出しは、ハンドラの前に検証される。`listPlanets` のように無ければ引数なし
- `.output` は必須ではない。クライアントの戻り値型はハンドラの return から流れる
- `router.planet.find` が、クライアントの `orpc.planet.find({ id: 1 })` になる

`os` は oRPC server の略。チェーンで middleware・context・typed error を足せるが、導入では「input と handler」だけでよい。

### まだ HTTP ではない

このファイルには URL も `GET` もない。HTTP への翻訳は `RPCHandler` の仕事である（次のレッスン）。ここが Hono の `app.get('/posts/:id', ...)` との発想の違いである。Hono は最初からパスがある。oRPC は最初に関数の木がある。

データベースや認証はこの段階では書かない。書いた瞬間に「procedure の中に何を置くか」へ話が飛ぶ。導入のゴールは、木の形と input の位置だけである。

## 確認

- `findPlanet` に `{ id: '1' }` を渡したとき、ハンドラ本体は走るか（スキーマが number の場合）
- `router` をネストする意味は、URL 設計か、クライアントの呼び出しパスか
- なぜこのファイルに `app.get` が出てこないか

## 次へ

- [03 Client と Hono](./03-client-and-hono.md)

## 公式

- [Getting Started — Define a Router](https://orpc.dev/docs/getting-started)
- [Procedure](https://orpc.dev/docs/procedure)
- [Router](https://orpc.dev/docs/router)
