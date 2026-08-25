# フロントエンドのエコシステム

個別のライブラリを追う前に、**層**を持つ。React も Vite も Hono も、同じ層の別の選択ではなく、違う仕事をしている。

## ゴール

- ランタイム / バンドラ / UI / メタフレームワーク / データ取得を区別できる
- 「このツールはどの層か」と聞かれたときに、地図のどこかを指せる
- 次の Hono / oRPC トラックが、どの層の話かわかる

## 前提

- HTML / CSS / JavaScript のごく基本
- ブラウザで URL を開くと何かが表示される、という経験

## レッスン

1. [01 全体地図](./01-map.md) — ツールを層に分ける
2. [02 リクエストが画面になるまで](./02-request-lifecycle.md) — 1 回のアクセスで何が起きるか

## 予定（未執筆）

- TypeScript がフロントとサーバーで共有される理由
- メタフレームワーク（Next / TanStack Start など）が隠している境界
- モノレポで API と UI を並べるときの型の流れ

## 公式

- [MDN Web 開発入門](https://developer.mozilla.org/ja/docs/Learn_web_development)
- [Vite ガイド](https://vite.dev/guide/)
