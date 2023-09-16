# あいまいなリクエストによる Web キャッシュ ポイズニング(クリア不可)

以下のハッキングラボを実施  
https://portswigger.net/web-security/host-header/exploiting/lab-host-header-web-cache-poisoning-via-ambiguous-requests  
このラボは、キャッシュとバックエンド アプリケーションがあいまいなリクエストを処理する方法に矛盾があるため、Web キャッシュ ポイズニング に対して脆弱です。

※現状、複数の Host ヘッダーがあるリクエストに対して、以下が返るのでクリア不可の模様

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8
X-Content-Type-Options: nosniff
Connection: close
Content-Length: 50

{"error":"Duplicate header names are not allowed"}
```
