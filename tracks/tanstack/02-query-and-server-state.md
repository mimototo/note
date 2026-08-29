# Query とサーバー状態

## このレッスンでわかること

- Next の Server Components の `fetch` と、TanStack Query は、同じ「データ取得」でもキャッシュの寿命が違う
- Query が置き換えるのは Redux に載せたサーバー状態であり、UI の `useState` ではない
- 両方を同じアプリで使う理由（最初の HTML と、その後のクライアント同期）
- `staleTime` が、Next の暗黙キャッシュへの別解になっていること

## 前提

- [01 なぜ TanStack か](./01-why-tanstack.md)
- [リクエストが画面になるまで](../frontend-ecosystem/02-request-lifecycle.md) の CSR / SSR

## 本文

サーバーにあるデータは、画面の `count` とは性質が違う。向こうで他の人が変えうる、非同期でしか来ない、すぐ古くなる。Query の公式が「server state」と呼ぶのはこの塊である。テーマ色やサイドバーの開閉は client state のまま、`useState` や小さなストアで足りる。

### Next.js が解いている時刻

App Router の Server Component は、リクエストのたびにサーバーで非同期関数として走り、HTML にデータを埋め込める。

```tsx
export default async function Page() {
  const res = await fetch('https://api.example.com/posts')
  const posts: { id: string; title: string }[] = await res.json()
  return (
    <ul>
      {posts.map((p) => (
        <li key={p.id}>{p.title}</li>
      ))}
    </ul>
  )
}
```

ここで起きているのは、**この HTTP リクエストに対する HTML を組み立てる**ことである。Next 15 以降、上の `fetch` はデフォルトではキャッシュしない。同じ木の中の同一 URL は React がメモ化するが、それは「この一回の描画で二重に撃たない」であり、次のリクエストや、ブラウザに残った SPA 状態とは別である。長く持たせたいときは `use cache` や `cache: 'force-cache'`、`revalidate` など、**フレームワークのキャッシュ API** を明示する（現行の名前は公式の Fetching data / Caching を見る）。

クライアントでボタンを押すたびにリストを最新化したい、タブを戻したら背景で再取得したい、一覧と詳細で同じ `postId` を共有したい、は、この「リクエスト単位の HTML」だけでは足りない。公式も Client Component では SWR や React Query を挙げている。

### Query が解いている時刻

Query はブラウザ（と、ハイドレーション後の React ツリー）に、**キー付きのサーバー状態キャッシュ**を置く。

```tsx
import { useQuery } from '@tanstack/react-query'

function Posts() {
  const { isPending, error, data } = useQuery({
    queryKey: ['posts'],
    queryFn: () =>
      fetch('https://api.example.com/posts').then((r) => r.json()),
    staleTime: 60_000,
  })

  if (isPending) return <p>Loading...</p>
  if (error) return <p>{error.message}</p>

  return (
    <ul>
      {data.map((p: { id: string; title: string }) => (
        <li key={p.id}>{p.title}</li>
      ))}
    </ul>
  )
}
```

`queryKey` がキャッシュの住所である。同じキーを複数コンポーネントが読めば、ネットワークは一本にまとまる。`staleTime` は「この間は新鮮とみなす」。切れたあともすぐ消さず、画面には出しつつ裏で取り直す（stale-while-revalidate）。フォーカス復帰で取り直す、失敗を表示する、といったサーバー状態の配線が、フックの戻り値に乗っている。

Next の「なぜ古い／なぜ新しい」が複数レイヤのデフォルトだったのに対し、ここは **このクエリのオプション** として残る。01 で見た不満のうち、キャッシュの暗黙さへの別解がここである。

### 競合に見える理由、競合でない理由

どちらも「posts を取る」。だから乗り換え話になる。時刻が違う。

| | Next の Server `fetch` | TanStack Query |
| --- | --- | --- |
| 主に効く瞬間 | 最初の HTML、SEO、サーバーで秘密を使う | ハイドレーション後の同期、画面をまたぐ共有 |
| キャッシュの置き場 | フレームワーク / CDN / リクエスト内メモ化 | クライアントの QueryClient（脱水すれば SSR からも渡せる） |
| 古いデータの扱い | ルート設定や `use cache` など枠の API | `staleTime` / `gcTime` / invalidate |
| `'use client'` | 不要（サーバーで await） | フックなのでクライアント（または専用の SSR 配線） |

ダッシュボードのフィルタ、無限スクロール、楽観的更新は Query の領分になりやすい。ブログの記事本文を HTML で返して終わらせるなら、Server Component の `await` で足りることが多い。

同じアプリで両方使う、が実務の多数派である。サーバーで取った結果を Query の `initialData` や脱水（dehydrate / hydrate）に載せ、最初の描画は HTML、そのあとの再取得と共有は Query、という合成である。公式の SSR ガイドがその配線である。Next を捨てる手順ではない。

Start や Router の `loader` も「ルートに入ったときのデータ」を持つ。Query 公式は、ルートのローダとクライアントキャッシュは役割が違う、と Router 側でも繰り返している。loader は遷移のゲート、Query は画面が生きているあいだのサーバー状態、と分けると混線しにくい。組み合わせの細部は予定レッスンと、Query の SSR / Start のガイドへ。

## 確認

- 同じ `fetch('.../posts')` でも、Server Component の `await` と `useQuery` では、キャッシュが誰の寿命か何が違うか
- サイドバーの開閉を `useQuery` に載せない理由は何か
- Next を使い続けたまま Query を足すことは、01 のどの取り違えを避けるか

## 次へ

- [03 Router と URL](./03-router-and-url.md)

## 公式

- [Query Overview](https://tanstack.com/query/latest/docs/framework/react/overview)
- [Does this replace client state?](https://tanstack.com/query/latest/docs/framework/react/guides/does-this-replace-client-state)
- [SSR](https://tanstack.com/query/latest/docs/framework/react/guides/ssr)
- [Next.js: Fetching data](https://nextjs.org/docs/app/getting-started/fetching-data)
- [Next.js 15 Caching](https://nextjs.org/blog/next-15)
