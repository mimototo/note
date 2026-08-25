# Go

コンパイルする言語で、標準ライブラリの `net/http` だけで HTTP サーバーを書ける。Hono で触った「メソッドとパスと Response」を、JS ランタイムの外でやり直すトラックである。

## ゴール

- Go がサーバーに選ばれやすい理由を、実行モデルの言葉で言える
- モジュール（`go.mod`）と、`func` / 構造体の最低限が読める
- `net/http` で Hono の Hello 相当を読める

## 前提

- [HTTP を分解する](../networking/02-http.md) があると対応が速い
- どれか一つの言語で関数を書いたことがある

## レッスン

1. [01 なぜ Go か](./01-why-go.md) — コンパイル、標準ライブラリ、goroutine
2. [02 モジュールと最初のサーバー](./02-module-and-http.md) — `go.mod` と `net/http`

## 予定（未執筆）

- `context.Context` とキャンセル
- テーブル駆動テスト
- インタフェースと小さいパッケージ
- JSON と `encoding/json`
- モジュールのバージョンと `go.sum`

## 公式

- [Go ドキュメント](https://go.dev/doc/)
- [Tutorial: Get started](https://go.dev/doc/tutorial/getting-started)
- [net/http](https://pkg.go.dev/net/http)
