# モジュールと最初のサーバー

## このレッスンでわかること

- `go.mod` が「このディレクトリは一つのモジュール」という宣言であること
- `package main` と `func main` が実行入口であること
- `net/http` が、Hono の `app.get` + listen に相当すること

## 前提

- [01 なぜ Go か](./01-why-go.md)
- マシンに Go が入っている必要はない。読むだけでもよい。動かすなら [インストール](https://go.dev/doc/install)

## 本文

モジュールの初期化:

```bash
go mod init example.com/hello
```

`go.mod` にモジュールパスと Go の版が書かれる。依存を足すと `go get` し、`go.sum` にチェックサムが貯まる。Node の `package.json` + lock に近い役割だが、ツールは `go` コマンドに同梱である。

最小の実行ファイル:

```go
package main

import "fmt"

func main() {
	fmt.Println("hello")
}
```

`package main` がコマンドになる。ライブラリにするときは別のパッケージ名にする。

### HTTP

```go
package main

import (
	"encoding/json"
	"log"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		if r.Method != http.MethodGet {
			http.Error(w, "method not allowed", http.StatusMethodNotAllowed)
			return
		}
		w.Header().Set("Content-Type", "text/plain; charset=utf-8")
		_, _ = w.Write([]byte("hello"))
	})

	http.HandleFunc("/api", func(w http.ResponseWriter, r *http.Request) {
		w.Header().Set("Content-Type", "application/json")
		_ = json.NewEncoder(w).Encode(map[string]string{"message": "Hello!"})
	})

	log.Fatal(http.ListenAndServe("127.0.0.1:3000", nil))
}
```

対応:

| Hono | Go `net/http` |
| --- | --- |
| `c.req` | `*http.Request` |
| 戻り値の `Response` | `http.ResponseWriter` に書き込む |
| `c.json` | ヘッダ + `json.Encoder` |
| `export default app`（Workers） | `ListenAndServe` で自分が見る |

Hono はハンドラが `Response` を **return** する。Go の古典的な `ServeMux` は Writer に **書き込む**。同じ HTTP でも API の形が違う。`r.URL.Path`、`r.Header.Get`、`r.Body` がネットワークトラックで見た部品である。

`ListenAndServe("127.0.0.1:3000", nil)` の `nil` はデフォルトのマルチプレクサ（`http.HandleFunc` で登録したもの）を使う、という意味。本番ではタイムアウト付きの `http.Server` を自分で組むことが多い。導入では「ポートを聞いて、パスで分岐する」が見えればよい。

実行:

```bash
go run .
```

## 確認

- `package main` ではないファイルだけを `go run` してコマンドにできるか（通常の話）
- `ResponseWriter` に書き込むモデルと、Hono が `Response` を返すモデルの違いを一文で
- `127.0.0.1:3000` の `3000` は、ネットワークのどの層の番号か

## 次へ

- 箱に入れる → [Docker](../docker/)
- 予定（context, テスト）は [トラック README](./README.md)

## 公式

- [Create a Go module](https://go.dev/doc/tutorial/create-a-module)
- [net/http](https://pkg.go.dev/net/http)
- [Writing Web Applications](https://go.dev/doc/articles/wiki/)
