# TanStack と Next.js

TanStack は一つのフレームワークではなく、**層ごとに選べるライブラリ群**である。Next.js は React 向けのメタフレームワークとして、ルーティング・SSR・データ取得・ビルドをまとめて引き受ける。同じ「React で画面を出す」でも、境界の引き方が違う。

このトラックは、Next.js への不満の中身と、TanStack がどこを別解にしているかを、層を混ぜずに読む。

## ゴール

- 「TanStack」と聞いたときに Query / Router / Start のどれかを指せる
- Next.js がまとめて隠している境界（キャッシュ、サーバー / クライアント、URL）を説明できる
- Query は Next の競合ではなく、Router / Start は一部が競合だと言える
- 自分のアプリが「HTML をサーバーで組む」比重か「URL とクライアントキャッシュ」比重かで、どちらが効くか判断できる

## 前提

- 先に [図解 HTML](./illustrated.html) をブラウザで開く（GitHub 上ではソースになる）
- [フロントエンドの地図](../frontend-ecosystem/01-map.md) の層（UI / メタフレームワーク / データ取得）
- React のコンポーネントと `fetch` が読める
- Next.js を本番で使った経験は不要。App Router の話は本文で足りる

## レッスン

1. [図解 HTML](./illustrated.html) — ブラウザで層を見る（このファイルを開く）
2. [01 なぜ TanStack か](./01-why-tanstack.md) — Next への不満と、ライブラリ群という答え方
3. [02 Query とサーバー状態](./02-query-and-server-state.md) — フレームワークの `fetch` キャッシュと、明示的なクライアントキャッシュ
4. [03 Router と URL](./03-router-and-url.md) — App Router の規約と、型の通るルート木
5. [04 Start という枠](./04-start-as-framework.md) — メタフレームワーク同士。何を捨て、何を残すか

## 予定（未執筆）

- TanStack Query と oRPC / ルート loader の組み合わせ
- Table / Form / Virtual（ヘッドレス UI。Next とは別層）
- Start の server routes と Hono を並べるとき

## 公式

- [TanStack](https://tanstack.com/)
- [TanStack Query](https://tanstack.com/query/latest)
- [TanStack Router](https://tanstack.com/router/latest)
- [TanStack Start](https://tanstack.com/start/latest)
- [Next.js App Router](https://nextjs.org/docs/app)
