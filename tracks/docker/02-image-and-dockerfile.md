# イメージと Dockerfile

## このレッスンでわかること

- Dockerfile はイメージを積む手順書であること
- `FROM` / `COPY` / `RUN` / `CMD` の役割
- ポートは Dockerfile だけではホストに開かないこと

## 前提

- [01 なぜコンテナか](./01-why-containers.md)

## 本文

イメージは層の積み重ねである。各命令が、前の層の上に差分を載せる。よく出る命令:

| 命令 | すること |
| --- | --- |
| `FROM` | 土台のイメージ（例: `node:22-alpine`, `golang:1.23`） |
| `WORKDIR` | 以降の作業ディレクトリ |
| `COPY` | ビルド文脈のファイルをイメージへ |
| `RUN` | ビルド時にコマンドを実行（`npm ci` や `go build`） |
| `ENV` | 環境変数 |
| `EXPOSE` | 「このポートを使う予定」という文書。実際の公開ではない |
| `CMD` | コンテナ起動時の既定コマンド |

Hono（Node）のイメージの骨格例。版は公式 Node イメージに合わせること。

```dockerfile
FROM node:22-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
CMD ["node", "dist/index.js"]
```

Go ならビルドしてバイナリだけ残す、がよくある（マルチステージは予定）。導入では「土台を選び、ファイルを入れ、依存を入れ、起動コマンドを書く」が見えればよい。

ビルドと実行のイメージ:

```bash
docker build -t hello-api .
docker run --rm -p 3000:3000 hello-api
```

`-p ホスト:コンテナ` が TCP の扉をつなぐ。`EXPOSE 3000` だけ書いても、ホストのブラウザは届かないことが多い。ネットワークトラックの「ポートは扉」と同じ事実である。

### 層とキャッシュ

`COPY package.json` を先にし `npm ci` し、あとで `COPY . .` するのは、ソースだけ変えたときに依存層を再利用するためである。命令の順番は速さに効く。正しさより先に、「上から順に層が固定される」と知っておく。

### CMD とプロセス

コンテナのメインプロセスが終了すると、コンテナも終わる。サーバーはフォアグラウンドで listen し続ける。`CMD npm start` のシェル形より、JSON 形 `CMD ["node", "dist/index.js"]` のほうがシグナルの扱いが素直、という話は後でよい。今は「起動したらプロセスが残るか」だけ見る。

## 確認

- `EXPOSE 3000` と `docker run -p 3000:3000` のどちらがホストから接続できるために必要か
- `COPY` と `RUN` の順序を逆にすると、キャッシュで何が損しうるか
- Go の単一バイナリが、Node の `node_modules` よりイメージを小さくしやすい理由は、01 のどれか

## 次へ

- Docker 導入はここまで。地図に戻る → [curriculum.md](../../curriculum.md)
- 予定（Compose, ボリューム）は [トラック README](./README.md)

## 公式

- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [docker build](https://docs.docker.com/reference/cli/docker/build/)
- [docker run](https://docs.docker.com/reference/cli/docker/container/run/)
