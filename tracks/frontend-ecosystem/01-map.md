# 全体地図

## このレッスンでわかること

- フロントエンド周辺のツールは、同じ仕事の競合ではなく層が違うことが多い
- ランタイム・モジュール変換・UI・ルーティング / データ・デプロイを分けて見られる
- Hono と oRPC が地図のどこに載るか

## 前提

- [トラック概要](./README.md)

## 本文

名前を集めると無限に増える。覚える対象は名前ではなく、**どの問題を解いているか**である。

```text
ブラウザ / Node / Bun / Deno / Cloudflare Workers     ← コードを実行する場所（ランタイム）
TypeScript                                             ← 言語（型）。実行前に JS へ落ちることが多い
Vite / esbuild / webpack                               ← モジュールをブラウザが読める形にする
React / Vue / Svelte / Solid                           ← UI をコンポーネントとして組む
Next / Nuxt / TanStack Start / SvelteKit               ← ルーティング・SSR・ビルドをまとめる
fetch / TanStack Query / tRPC / oRPC                   ← サーバーのデータを型つきで取る
Tailwind / CSS Modules                                 ← 見た目
Vercel / Cloudflare / Docker 上の自分のサーバー         ← 動かす場所
```

上から下は依存関係のイメージであって、必ず全部使うわけではない。静的な HTML だけなら、下の大半は不要である。

### よくある混同

- **React と Next.js**  
  React は UI ライブラリ。Next.js は React を使ったアプリケーション枠（ルーティング、SSR、バンドル）。「React をやめる」と「Next をやめる」は別の判断。

- **Vite と React**  
  Vite は開発サーバーとバンドラ。React は UI。Vite は Vue でも Svelte でも使える。

- **Node.js と npm**  
  Node は JS をサーバー（やツール）として動かすランタイム。npm はパッケージを取る道具。Bun や Deno はランタイム側の別の選択。

- **Hono と React**  
  Hono は HTTP のリクエストを受けて Response を返す。React は画面を組む。同じ TypeScript でも層が違う。Hono の上に JSX を載せることはできるが、それは「Hono が React の代わり」ではない。

- **oRPC と Hono**  
  Hono は「どの URL のどの HTTP メソッドか」を配線する。oRPC は「サーバーの関数を、クライアントから型付きで呼ぶ」契約。Hono に oRPC のハンドラを載せる、という組み合わせが自然。

### このリポジトリでの位置

| このあと学ぶもの | 地図上の層 |
| --- | --- |
| Hono | ランタイムの上で HTTP を扱う |
| oRPC | HTTP の上で API の型を端から端まで通す |
| ネットワーク | ランタイムより下。パケットと HTTP そのもの |
| Go | 同じ HTTP 層を、別の言語と標準ライブラリで |
| Docker | 上の全部を「どこで動かすか」に梱包する |

ツールの流行は速い。層の分け方は遅い。新しい名前が出たら、まず「どの層の置き換えか」を聞く。

## 確認

- Vite をやめても React コンポーネントの書き方が残ることがある。それはなぜか
- Hono は React の競合か。そう思わない理由を一層の言葉で言え
- 新しい「フルスタックフレームワーク」が出たとき、最初に確認する層はどれか

## 次へ

- [02 リクエストが画面になるまで](./02-request-lifecycle.md)

## 公式

- [MDN: ウェブの仕組み](https://developer.mozilla.org/ja/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works)
- [Hono](https://hono.dev/docs/)
- [oRPC](https://orpc.dev/)
