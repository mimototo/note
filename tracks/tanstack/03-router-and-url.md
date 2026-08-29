# Router と URL

## このレッスンでわかること

- Next の App Router は、ディレクトリと特別なファイル名がルートグラフである
- TanStack Router は、ファイルから **生成された route tree** が契約になり、`Link` の `to` が型を持つ
- search パラメータを「文字列の残り」にせず、ルートのスキーマにする理由
- ルーティングを替えても Query は残せる。ここが App Router との部分的な競合である

## 前提

- [02 Query とサーバー状態](./02-query-and-server-state.md)
- URL にパスと `?page=2` があること（[HTTP](../networking/02-http.md) があると楽）

## 本文

ルーティングの仕事は「この URL でどの画面か」だけではない。パスの ID、クエリ文字列、レイアウトの入れ子、遷移中の pending、エラー境界が、同じグラフに載る。Next も TanStack Router も、ファイル名からルートを切る点では近い。違うのは、**グラフが型として残るか、規約の側に消えるか**である。

### Next.js App Router

`app/posts/[postId]/page.tsx` が `/posts/:postId` になる。同じ階層の `layout.tsx` が共通枠、`loading.tsx` が Suspense、`error.tsx` がエラー境界、という特別ファイルが増えていく。並列ルートやインターセプトなど、ディレクトリ名の記号でグラフを足す。強力で、慣れると速い。代償は、アプリの形が **ファイルシステムの暗黙** になり、リンク先は文字列連結になりやすいことである。

```tsx
import Link from 'next/link'

export default async function Page({
  params,
}: {
  params: Promise<{ postId: string }>
}) {
  const { postId } = await params
  return (
    <div>
      <p>post {postId}</p>
      <Link href={`/posts/${postId}/edit`}>編集</Link>
    </div>
  )
}
```

`postId` は実行時の文字列である。存在しないパスへの `href` を、コンパイラは原則として止めない。Next の Typed Routes を足すと既知のパスへは寄せられるが、search をルートの契約として親子で推論するところまでは、このトラックが Router に置く売りである。`searchParams` はさらに、ページ番号もフィルタも全部 `string | string[] | undefined` として入り、パースは各ページの仕事になる。一覧のソートを URL に載せたいアプリほど、ここが 01 で書いた「ルートの型が後回し」になる。

データは 02 のとおり、この `page` がサーバーで `await` することが多い。ルートモジュールそのものがサーバーコンポーネントになり、クライアントが要る部品だけ `'use client'` で切る。

### TanStack Router

推奨はファイルベースである。プラグインがファイルを見て route tree を生成する。ファイルを置く点は Next に似るが、各ファイルは `createFileRoute` で **ルートオブジェクト** を export し、アプリは生成された tree を Router に渡す。規約で消えるのではなく、型の入口が残る。

```tsx
import { createFileRoute, Link } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: ({ params }) => fetchPost(params.postId),
  component: PostPage,
})

function PostPage() {
  const { postId } = Route.useParams()
  const post = Route.useLoaderData()
  return (
    <div>
      <h1>{post.title}</h1>
      <Link to="/posts/$postId/edit" params={{ postId }}>
        編集
      </Link>
    </div>
  )
}
```

`to` と `params` は、登録されたルートから推論される。リネームやタイポはビルドで落ちる。公式が「100% inferred TypeScript」と呼ぶ中心がここである。コードベースのルート定義も同居できる。生成物を自分が import するので、「ルーターにファイルを吸わせて終わり」より、境界が見える。

### search を状態として扱う

フィルタ・ページ・モーダルの開閉を URL に置くと、共有と戻るボタンが無料になる。Next ではそのオブジェクトを自分で組み立てることが多い。TanStack Router は `validateSearch` をルートに置き、パースと型をルート契約にする。

```tsx
import { createFileRoute } from '@tanstack/react-router'

type ProductSearch = {
  page: number
  q?: string
}

export const Route = createFileRoute('/products')({
  validateSearch: (search: Record<string, unknown>): ProductSearch => ({
    page: Number(search.page) || 1,
    q: typeof search.q === 'string' ? search.q : undefined,
  }),
  component: ProductsPage,
})

function ProductsPage() {
  const { page, q } = Route.useSearch()
  return (
    <p>
      page {page}
      {q ? `, q=${q}` : ''}
    </p>
  )
}
```

`Link` や `navigate` の `search` もこの戻り値の型に揃う。本番では Zod などを `validateSearch` に載せ、文字列のクエリを数値へ落とす（公式は Standard Schema 互換のアダプタを案内する）。親子ルートで search を継承できる。公式が search を「アプリでいちばん強い状態」と呼ぶのは、ブックマークできるグローバル状態だからである。ダッシュボードで Next に不満が出やすい地点と、ちょうど重なる。

### データ読み込みとの役割分担

Router にも loader と、軽い SWR 風キャッシュがある。それでも公式は、本格的なサーバー状態は Query などクライアントキャッシュに寄せる設計だと言う。loader は「このルートを開いてよいか、初回に何が要るか」。Query は「開いたあとも古くなるデータ」。02 の表と同じ切り方である。

Next の App Router をやめて Router だけ Vite に載せる、はあり得る。そのとき Query は残る。Start は、この Router の契約を変えずに SSR とサーバー関数を足す（04）。だから「TanStack 対 Next」の真ん中の層は **Router** であり、Query ではない。

## 確認

- Next の文字列 `href` と、Router の `to` + `params` では、存在しないパスをいつ落とせるか
- `?page=2&q=` を各コンポーネントで `URLSearchParams` するのと、`validateSearch` では何が単一の正本になるか
- TanStack Router に替えても TanStack Query を外さなくてよい理由は何か

## 次へ

- [04 Start という枠](./04-start-as-framework.md)

## 公式

- [TanStack Router Overview](https://tanstack.com/router/latest/docs/framework/react/overview)
- [File-Based Routing](https://tanstack.com/router/latest/docs/framework/react/routing/file-based-routing)
- [Search Params Are State](https://tanstack.com/blog/search-params-are-state)
- [Next.js: Linking and Navigating](https://nextjs.org/docs/app/getting-started/linking-and-navigating)
- [Next.js: page.js](https://nextjs.org/docs/app/api-reference/file-conventions/page)
