# Start という枠

## このレッスンでわかること

- TanStack Start は Router のアプリモデルを残したまま、SSR とサーバー関数とビルドを足す
- Next の Server Actions / RSC 中心と、Start の「ルート + `createServerFn`」では、見える境界が違う
- Start は執筆時点で Release Candidate。採用は Query / Router より新しいリスクがある
- Next を残す理由と、Start に寄せる理由を、層の言葉で言い分けられる

## 前提

- [03 Router と URL](./03-router-and-url.md)
- [なぜ Hono か](../hono/01-why-hono.md) の「入口とアプリ本体」

## 本文

03 までの Router は、Vite 上の CSR でも使える。足りないのは、最初のドキュメントをサーバーが組むこと、サーバーだけの秘密でデータを取ること、それを本番のランタイムへ出すことである。TanStack Start はその欠けを、**Router を置き換えずに**足す。公式の言い方では、ルート・params・search・loader・Link は Router のまま、Start が SSR・streaming・server functions・デプロイ出力を被せる。

執筆時点の公式は Start を Release Candidate と書く。API は安定寄り、バグゼロではない。v1 前の枠である。Query や Router より、ここだけ新しさのコストがある。

### サーバー仕事を関数として置く

Next の Server Actions は `'use server'` の関数をフォームや呼び出しから渡す。Start は `createServerFn` で、検証と handler を明示する。クライアントから呼ぶとビルドが RPC に差し替え、サーバー実装はバンドルに残さない。

```tsx
import { createServerFn } from '@tanstack/react-start'
import { createFileRoute } from '@tanstack/react-router'
import { z } from 'zod'

const getPost = createServerFn({ method: 'GET' })
  .validator(z.object({ id: z.string() }))
  .handler(async ({ data }) => {
    const res = await fetch(`https://api.example.com/posts/${data.id}`)
    const post: { id: string; title: string } = await res.json()
    return post
  })

export const Route = createFileRoute('/posts/$postId')({
  loader: ({ params }) => getPost({ data: { id: params.postId } }),
  component: PostPage,
})

function PostPage() {
  const post = Route.useLoaderData()
  return <h1>{post.title}</h1>
}
```

`getPost` は loader からも、クライアントのイベントからも、同じ型で呼べる。外部の公開 API にしたいなら server function ではなく server routes、と公式は分ける。Hono や oRPC が解く「HTTP の入口」と、アプリ内 RPC を混ぜない、という切り方である。CSRF や same-origin の前提も Start のガイドにある。枠が隠すのではなく、関数とルートという名前で残す。

Next の RSC は「このコンポーネントはサーバー」が合成の単位になる。Start にも Server Components の経路はあるが、背骨はルート木と server function である。01 の不満（ファイル属性としての境界、暗黙キャッシュ）に対する別解は、「コンポーネントの実行場所」より **呼び出すサーバー関数と、Router の loader** を先に読む、である。

ビルドは Vite または Rsbuild。デプロイ先は Nitro などの出力プラグインで変える話になり、ルートの書き方は変えない、と公式は Universal Deployment を売る。Next の `next build` とホスト固有の最適化に対する、インセンティブ側の別解である。摩擦がゼロ、ではない。エコシステム（画像、フォント、事例、求人）は Next が厚い。

### いつ Next のままか、いつ Start を見るか

層を戻す。

- **Query だけ** — Next のままが普通。Client のサーバー状態が足りないとき。乗り換えではない
- **Router** — URL とリンクの型、search を契約にしたい。Vite SPA ならここで足りることがある
- **Start** — その Router アプリに SSR とサーバー関数が要るとき。Next と同層の選択

Next を残すのが合理になりやすい条件:

- 最初の HTML とキャッシュ、画像、フォント、事例の厚みが製品の速度になる
- チームと採用が App Router 前提
- RSC で JS を薄くする、がコンテンツサイトの主戦場

Start（と Router）を見るのが合理になりやすい条件:

- すでに Query があり、URL 状態が複雑（03 の search）
- キャッシュとサーバー境界を、枠のデフォルトではなくコードの名前で追いたい
- ビルドとホストを Vite 生態系に寄せたい
- RC と、小さなエコシステムを自分で飲める

どちらを選んでも、[Hono](../hono/) の HTTP と [oRPC](../orpc/) の手続きは下の層に残せる。Start の server function はアプリ内の型付き呼び出しであり、公開 HTTP の代わりではない。地図の「メタフレームワークが隠す境界」を、Start は Router と関数の名前で開いて見せる、というのがこのトラックの終わりである。隠さないことが常に正しいわけではない。隠して速いのが Next の勝ちである。自分のアプリが、隠された境目で何度止まっているかだけを見る。

## 確認

- Start を入れても、ルートの `to` や `validateSearch` の正本はどのライブラリか
- `createServerFn` と Hono の `app.get` は、同じ「サーバーで走る」でも誰向けの入口か
- Next をやめて Start にする判断と、Next の中に Query を足す判断を、一言で区別せよ

## 次へ

- 地図に戻る → [frontend-ecosystem](../frontend-ecosystem/)
- HTTP の入口を先に固める → [Hono](../hono/)
- 公開 API の型 → [oRPC](../orpc/)

## 公式

- [TanStack Start Overview](https://tanstack.com/start/latest/docs/framework/react/overview)
- [Server Functions](https://tanstack.com/start/latest/docs/framework/react/guide/server-functions)
- [Migrate from Next.js](https://tanstack.com/start/latest/docs/framework/react/migrate-from-next-js)
- [Next.js: Server Actions](https://nextjs.org/docs/app/getting-started/mutating-data)
- [Vite](https://vite.dev/guide/)
