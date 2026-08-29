# なぜ TanStack か

## このレッスンでわかること

- Next.js への不満は「React が嫌い」ではなく、**全部入りが隠す境界**への不満が多い
- TanStack は Next の別名ではなく、Query・Router・Start で層が違う
- 人気の入口は Start ではなく Query である。Next の中で Query を使うのが普通
- 明示的なキャッシュ・型の通る URL・Vite 上の枠、が別解として揃ってきた理由

## 前提

- 先に [図解 HTML](./illustrated.html) をブラウザで開く（GitHub 上ではソースになる）
- [トラック概要](./README.md)
- [全体地図](../frontend-ecosystem/01-map.md) の「メタフレームワーク」と「データ取得」

## 本文

Next.js は、ファイルを置くとルートになり、サーバーで HTML を返し、必要なら API も同じリポジトリに書ける、という約束で広く使われた。React 単体では自分で揃える仕事（ルーティング、SSR、バンドル、画像）を、一つの枠が引き受ける。採用される理由は今もこれである。

文句が出るのも、同じ場所からである。枠が仕事を引き受けるほど、**いつサーバーで走り、いつキャッシュされ、URL のどの部分が型を持つか**が、コード上の名前ではなく「規約とデフォルト」になる。デフォルトが自分のアプリとずれると、直す対象が自分の関数ではなくフレームワークの暗黙になる。

### Next.js に繰り返し向けられる不満

全部が「Next が壊れている」ではない。現場で同じ種類の摩擦として語られやすいものを、層に分けて書く。

1. **キャッシュが先に決まる**  
   App Router 初期（とくに Next 13〜14）は、`fetch` やルートを「キャッシュする側」に寄せた。開発の `next dev` と本番で見え方が違い、「なぜ古いデータが出るか」がフレームワークの複数キャッシュ（Data Cache、Full Route Cache、Router Cache）の合成になった。Next.js 15 は公式に、`fetch` や GET Route Handler、クライアントの Router Cache のデフォルトを **キャッシュしない側** へひっくり返した。デフォルトを変えるほど、暗黙キャッシュは現場のコストだった、という記録である。

2. **サーバーとクライアントの境界がファイルの属性になる**  
   React Server Components では、デフォルトはサーバー、`'use client'` でクライアントに降りる。日付や関数を props で渡せない、イベントが要る部品だけクライアント、といった分割が日常になる。これは React のモデルだが、Next はこれを **アプリの標準経路** にした。インタラクティブな画面ほど、「このファイルはどっちで動くか」が先に来る。

3. **ルートの型が後回し**  
   `<Link href={...}>` の先は文字列になりやすい。`searchParams` は `string | string[] | undefined` を自分で削る。フィルタやページ番号が URL に乗るアプリほど、ここがバグの出どころになる。Typed Routes でパスは寄せられるが、search を「ルートの契約」として推論する強さは、後述の Router とは別物である。

4. **Pages Router と App Router が並んだ期間が長い**  
   同じ「Next」でも書き方が二つある。パッケージ、ブログ、同僚の記憶がどちらを指すかでコストが出る。枠の強さ（一つの名前で全部やる）の裏側である。

5. **ホスティングとの距離**  
   コードは OSS で、自前ホストもできる。一方、開発の主導は Vercel にあり、画像最適化や一部ランタイムはホストによって厚さが違う。『枠がデプロイ先の導線でもある』という見方は、陰謀ではなくインセンティブの話としてよく出る。

メタフレームワークは、[リクエストの流れ](../frontend-ecosystem/02-request-lifecycle.md) の 4〜6 を一つの製品に畳む。畳んだ境目が見えなくなると、上の不満になる。

### TanStack は一つのライバルではない

名前が同じ会社のツールでも、地図上の場所が違う。

```text
TanStack Query     サーバー状態のクライアントキャッシュ（Next の中でも使う）
TanStack Router    型の通るルーティングと URL 状態（App Router の一部の別解）
TanStack Start     Router を核にしたメタフレームワーク（Next と同層）
Table / Form など  ヘッドレス UI（このトラックの本題ではない）
```

「TanStack に乗り換える」は、たいていこのどれかを取り違えている。Query を入れるのは Next をやめることではない。Start を選ぶのは、枠そのものを替えることである。

人気の順番も、この取り違えを解く。先に広まったのは **Query**（旧 React Query）である。Redux でサーバーから来た JSON をローディング付きで運ぶ作業を、`queryKey` と `queryFn` に畳んだ。Next の Server Components が主流になる前から、クライアント側の「サーバー状態」の事実上の標準になった。Next 公式の Client Component 向けデータ取得でも、コミュニティライブラリとして Query や SWR が挙がる。信頼が先にあり、同じ作者の Router と、その上の Start が「次に試す枠」として見える、という流れである。

### 別解が売っているもの

TanStack 側が Next の全部入りに対して置いている軸は、だいたい次である。

- **合成できる** — Query だけ、Router だけ、と足せる。枠に入らないとルーティングもキャッシュも手に入らない、ではない
- **暗黙より名前** — `staleTime` は「このデータは何ミリ秒鮮度を保つか」と読む。ルートは生成された route tree として残る。`'use client'` の代わりに、server function は `createServerFn` と書いて呼ぶ
- **URL を契約にする** — パスパラメータも search も、ルート定義から型が流れる（03）
- **ビルドを Vite / Rsbuild に寄せる** — Start は「Router のアプリモデルはそのまま、出力と SSR を足す」。独自コンパイラがアプリの契約、になりにくい

Next が弱い、ではない。コンテンツが多く、最初の HTML にデータを載せたい、採用と事例が要る、Vercel に載せる、なら全部入りは今も合理である。ダッシュボードのように **同じセッションで URL とキャッシュを細かく動かす** アプリほど、Query と Router の明示が効きやすい。枠を替えるかどうかは 04 まで置いてよい。多くのチームは、Next のまま Query だけ足す。

## 確認

- 「TanStack に乗り換えた」と言われたとき、まず聞き返す層はどれか。Query か Start か
- Next 15 が `fetch` のデフォルトキャッシュをやめたことは、不満のどの種類への反応か
- Next の中で TanStack Query を使うことは、メタフレームワークの乗り換えか

## 次へ

- [02 Query とサーバー状態](./02-query-and-server-state.md)

## 公式

- [TanStack Query Overview](https://tanstack.com/query/latest/docs/framework/react/overview)
- [TanStack Start Overview](https://tanstack.com/start/latest/docs/framework/react/overview)
- [Next.js 15: Caching のデフォルト変更](https://nextjs.org/blog/next-15)
- [Next.js: Fetching data](https://nextjs.org/docs/app/getting-started/fetching-data)
