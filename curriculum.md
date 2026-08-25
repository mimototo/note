# 学習地図

上から順に進むと、あとで出てくる道具の「なぜ」がつながります。飛ばしても戻れます。

## 進捗

読んだレッスンのチェックを自分で入れてください。

### 1. フロントエンドの地図

今の Web 開発が何層に分かれているかを先に持つ。個別のツール名を暗記する段階ではない。

- [ ] [tracks/frontend-ecosystem](./tracks/frontend-ecosystem/)
  - [ ] [01 全体地図](./tracks/frontend-ecosystem/01-map.md)
  - [ ] [02 リクエストが画面になるまで](./tracks/frontend-ecosystem/02-request-lifecycle.md)

### 2. Hono

地図の「サーバー / HTTP」層を、Web Standards の薄いフレームワークで具体化する。

- [ ] [tracks/hono](./tracks/hono/)
  - [ ] [01 なぜ Hono か](./tracks/hono/01-why-hono.md)
  - [ ] [02 Context と Response](./tracks/hono/02-context.md)
  - [ ] [03 ルーティング](./tracks/hono/03-routing.md)
  - [ ] [04 ミドルウェア](./tracks/hono/04-middleware.md)

### 3. oRPC

HTTP の上に「関数呼び出しのように型が通る API」を載せる。Hono と組み合わせるところまでが導入。

- [ ] [tracks/orpc](./tracks/orpc/)
  - [ ] [01 なぜ oRPC か](./tracks/orpc/01-why-orpc.md)
  - [ ] [02 Procedure と Router](./tracks/orpc/02-procedure-and-router.md)
  - [ ] [03 Client と Hono](./tracks/orpc/03-client-and-hono.md)

### 4. ネットワーク

ここまで使ってきた HTTP の下を見る。Hono の `Context` や Docker のポートが、何の上に乗っているかがわかる。

- [ ] [tracks/networking](./tracks/networking/)
  - [ ] [01 層で見る通信](./tracks/networking/01-layers.md)
  - [ ] [02 HTTP を分解する](./tracks/networking/02-http.md)

### 5. Go

同じ「HTTP サーバー」を、JavaScript ではない言語で書く。標準ライブラリの厚さと並行処理の発想が目的。

- [ ] [tracks/go](./tracks/go/)
  - [ ] [01 なぜ Go か](./tracks/go/01-why-go.md)
  - [ ] [02 モジュールと最初のサーバー](./tracks/go/02-module-and-http.md)

### 6. Docker

今まで動かしていたプロセスを、再現可能な箱に入れる。

- [ ] [tracks/docker](./tracks/docker/)
  - [ ] [01 なぜコンテナか](./tracks/docker/01-why-containers.md)
  - [ ] [02 イメージと Dockerfile](./tracks/docker/02-image-and-dockerfile.md)

## このあとに足すとよいもの

教材としてまだないが、地図の延長にあるもの。

- Hono の Validator と RPC モード（`hc`）
- oRPC の OpenAPI / contract-first / TanStack Query
- Go の `context`, テスト, モジュール設計
- Docker Compose、ネットワーク、ボリューム
- TLS、DNS のキャッシュ、ロードバランサ

追加するときは [`AGENTS.md`](./AGENTS.md) に従って該当トラックへレッスンを足す。
