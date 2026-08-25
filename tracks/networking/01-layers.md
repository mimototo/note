# 層で見る通信

## このレッスンでわかること

- よく混ぜられる五つの仕事（名前、住所、接続、暗号化、書類）を分けられる
- ブラウザの URL の各部分が、どの層の入力か
- Hono も Docker の `-p 8787:8787` も、このうちのどれを触っているか

## 前提

- [トラック概要](./README.md)

## 本文

`https://api.example.com:443/rpc` を分解する。

```text
https          書類のプロトコルは HTTP。運ぶ途中を TLS で守る、という約束
api.example.com 名前。DNS が IP アドレスに変える
443            その IP のどの扉か（ポート）。HTTPS の既定
/rpc           HTTP が見るパス。Hono や oRPC の prefix がここに来る
```

層を下から:

| 層 | 仕事 | 壊れたときの見え方の例 |
| --- | --- | --- |
| IP | パケットをホストまで届ける | タイムアウト、到達不能 |
| TCP | ポート同士の接続。順序と再送 | connection refused（誰もそのポートを聞いていない） |
| TLS | 暗号化と、証明書による相手の確認 | 証明書エラー |
| HTTP | メソッド・パス・ヘッダ・ボディ | 4xx / 5xx、CORS エラー |
| DNS | 名前 → IP | 名前解決に失敗 |

開発者が毎日触るのは上の二つ（HTTP と名前）が多い。Docker でコンテナの 3000 をホストの 3000 に出すのは、TCP の扉を繋いでいる。Hono のルートは HTTP のパスを見ている。混ぜると「CORS を Docker で直そう」のような的外れが起きる。

### 接続はリクエストより先

`fetch` を呼ぶと、まだ TCP の握手ができていなければ先に接続がある。keep-alive なら接続は使い回される。だから「HTTP リクエストが 1 回」と「TCP 接続が 1 本」は 1 対 1 ではない。導入では「書類の前に、住所と扉と（多くの場合）暗号化がある」で十分。

### ループバック

`127.0.0.1` と `localhost` は自分自身。oRPC の Getting Started が `127.0.0.1:3000` を聞くのは、同じマシンのそのポートに HTTP を届けるためである。別コンテナから見るときは `localhost` が「そのコンテナ自身」を指し、意外と届かない。それは Docker トラックのネットワークの話になる。

## 確認

- `connection refused` と `404` は、上の表のどの層か
- Hono の `app.get('/rpc', ...)` は IP アドレスを決めているか
- `https` の `s` が主に担当する層はどれか

## 次へ

- [02 HTTP を分解する](./02-http.md)

## 公式

- [MDN: ウェブの仕組み](https://developer.mozilla.org/ja/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works)
- [MDN: ポート](https://developer.mozilla.org/ja/docs/Glossary/Port)
