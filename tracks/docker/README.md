# Docker

プロセスと、それが必要とするファイルシステム・環境変数・ポートを、イメージという雛形からコンテナとして起こす。Hono も Go も「動かす場所」が違うと依存が壊れる。その再現性の層。

## ゴール

- イメージとコンテナと仮想マシンを混ぜない
- Dockerfile の主な命令が、イメージの層を積んでいるとわかる
- ポート公開が、ネットワークトラックの TCP であること

## 前提

- [層で見る通信](../networking/01-layers.md)
- どれかの言語でサーバーを listen したイメージ（Hono か Go で十分）

## レッスン

1. [01 なぜコンテナか](./01-why-containers.md) — 再現性と隔離
2. [02 イメージと Dockerfile](./02-image-and-dockerfile.md) — 層、FROM、CMD

## 予定（未執筆）

- `docker compose` で API と DB を並べる
- ボリューム（消えては困るデータ）
- コンテナ間 DNS（サービス名）
- マルチステージビルド（Go のバイナリを薄いイメージへ）
- 非 root ユーザーとイメージスキャンの入口

## 公式

- [Docker ドキュメント](https://docs.docker.com/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Overview of containers](https://docs.docker.com/get-started/docker-overview/)
