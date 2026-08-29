# リクエストが画面になるまで

## このレッスンでわかること

- ブラウザに URL を入れたあとに起きる段階を、ざっくり順に辿れる
- 「フロントのビルド」と「サーバーの HTTP」が、いつ交わるか
- あとで学ぶ DNS / TCP / HTTP / Hono / Docker が、この流れのどこか

## 前提

- [01 全体地図](./01-map.md)

## 本文

例として `https://example.com/app` を開く。SPA でも SSR でも、最初の数段は同じである。

```text
1. 名前を住所にする     DNS が example.com → IP アドレス
2. その住所へ接続する   TCP（その上で TLS）
3. 書類を渡す           HTTP リクエスト GET /app
4. 誰かが答える         プロセスが HTTP レスポンスを返す
5. 中身を解釈する       HTML / CSS / JS
6. 必要ならまた呼ぶ     fetch で JSON API、など
```

4 の「誰か」が Hono や Go のサーバーであり、それを動かしている箱が Docker や Cloudflare Workers である。

### 開発中に起きていること

ローカルで `vite` を起動しているとき、ブラウザが見ているのは多くの場合「Vite の開発サーバー」である。TypeScript や JSX は、保存のたびにブラウザが読める JS へ変換される。これは本番の Nginx や CDN とは別物だが、やっていることは同じ層（モジュールを実行可能な形にする）である。

API を別ポート（例: `http://localhost:8787`）で動かしているなら、画面用のサーバーと API 用のサーバーが二つある。CORS やプロキシが出てくるのは、ブラウザから見ると「別のオリジン」だからである。ネットワークのレッスンでオリジンを扱う。

### SSR と CSR

- **CSR（クライアントサイドレンダリング）**  
  最初の HTML はほぼ殻で、JS が起動してから画面とデータを組み立てる。Hono や oRPC は、そのあと JS が呼ぶ API 側に現れやすい。

- **SSR（サーバーサイドレンダリング）**  
  最初の HTML をサーバーが組み立てて返す。HTML を返すサーバーと JSON を返すサーバーは、同じプロセスでも別プロセスでもよい。

どちらでも、**HTTP で Request が来て Response が返る**こと自体は変わらない。Hono が扱うのはそこである。

### 型が端から端まで通る、の位置

oRPC や Hono の RPC モードが解決するのは、段階 6 付近である。「画面のコードが `user.id` を number だと思っているのに、サーバーは string を返した」を、ビルド時に落とす。DNS も TCP も、この型とは無関係である。層を混ぜない。

## 確認

- Vite の dev サーバーと、Hono の API サーバーは、上の 1〜6 のどれを担当しうるか
- CSR でも SSR でも共通している段階はどれか
- 「型安全な API」は、DNS の代わりになるか

## 次へ

- 地図はここまで。HTTP をコードにする → [Hono](../hono/)
- Next と TanStack が 4〜6 をどう畳むか → [TanStack](../tanstack/)
- この流れの下側を先に見たい → [ネットワーク](../networking/)

## 公式

- [MDN: ウェブの仕組み](https://developer.mozilla.org/ja/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works)
- [MDN: レンダリング](https://developer.mozilla.org/ja/docs/Web/Performance/Guides/How_browsers_work)
