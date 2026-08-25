# ネットワーク

Hono の `Context` も oRPC の `RPCLink` も、中身は IP の上の TCP（多くは TLS）の上の HTTP である。ツールのドキュメントを読む速度は、この層を一度自分の言葉で言えると上がる。

## ゴール

- 名前解決・接続・HTTP メッセージを混同しない
- メソッド / ステータス / ヘッダ / ボディが、Hono のどの API に対応するか
- 「ポート」「オリジン」「プロキシ」が次の Docker で何を指すか、先に言葉を持つ

## 前提

- [リクエストが画面になるまで](../frontend-ecosystem/02-request-lifecycle.md)
- Hono / oRPC を先に触っていると、対応関係が見える（必須ではない）

## レッスン

1. [01 層で見る通信](./01-layers.md) — DNS / IP / TCP / TLS / HTTP
2. [02 HTTP を分解する](./02-http.md) — メッセージの部品と Hono

## 予定（未執筆）

- オリジンと CORS
- TLS 証明書の役割（暗号化と「相手は誰か」）
- プロキシ、ロードバランサ、リバースプロキシ
- HTTP/2 と接続の再利用

## 公式

- [MDN: ウェブの仕組み](https://developer.mozilla.org/ja/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works)
- [MDN: HTTP](https://developer.mozilla.org/ja/docs/Web/HTTP)
- [MDN: URL](https://developer.mozilla.org/ja/docs/Web/API/URL)
