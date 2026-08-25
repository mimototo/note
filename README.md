# 学習リポジトリ

フロントエンドのエコシステムから、Hono / oRPC、Go、Docker、ネットワークまでを**体系的に学ぶ**ためのリポジトリです。

ここは日記やアイデア置き場ではありません。  
教材は AI が作り、人間が読んで確認する前提です。

## 使い方

1. [`curriculum.md`](./curriculum.md) で今どこにいるかを見る
2. 該当トラックの `README.md` からレッスンを順に読む
3. 各レッスン末尾の「確認」に自分の言葉で答える
4. 公式ドキュメントのリンクも必ず開く（このリポジトリは公式の要約ではなく、学び方の骨格）

新しいトピックを足したいときは、AI に「`tracks/<名前>` にレッスンを追加して」と頼んでください。書き方は [`AGENTS.md`](./AGENTS.md) にあります。

## トラック

推奨順は [学習地図](./curriculum.md) を見てください。興味があるところから入っても構いません。

| トラック | 何を学ぶか |
| --- | --- |
| [frontend-ecosystem](./tracks/frontend-ecosystem/) | ランタイム・バンドラ・UI・メタフレームワークの地図 |
| [hono](./tracks/hono/) | Web Standards 上の薄い Web フレームワーク |
| [orpc](./tracks/orpc/) | エンドツーエンドで型安全な API |
| [networking](./tracks/networking/) | IP / TCP / DNS / HTTP。上の全部の土台 |
| [go](./tracks/go/) | シンプルな言語でサーバーを書く |
| [docker](./tracks/docker/) | 実行環境をイメージとして梱包する |

## ディレクトリ

```text
tracks/          学習トラック（教材の本体）
templates/       レッスン・トラックの雛形
AGENTS.md        AI が教材を書くときのルール
curriculum.md    全体の学習地図と進捗
```
