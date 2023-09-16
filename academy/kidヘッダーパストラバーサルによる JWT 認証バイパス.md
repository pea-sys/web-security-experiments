# キッドヘッダーパストラバーサルによる JWT 認証バイパス

次のハッキングラボを実施します  
https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-kid-header-path-traversal  
このラボでは、セッションの処理に JWT ベースのメカニズムを使用します。署名を検証するために、サーバーは kidJWT ヘッダーのパラメーターを使用して、ファイル システムから関連するキーをフェッチします。

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

- 7. JWT Editor key タブを選択して、New Symmetric key 押下します。k パラメータを AA==(null)に設定して OK を押します。
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/636345f6-c56b-40e5-b01f-7d7f47d2dcd1)

* 8.Repeaterタブに戻り、JSON Web tokenタブを選択します
    * kidを../../../../../../../dev/nullに変更
    * subをadministratorに変更
![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/9cba70ff-9f9b-4737-bbcb-8112f583fa26)

* 9. 画面下部のSignボタンで生成したキーで署名します

- 10. リクエストを送信すると管理者ページが利用できます.
      一般ユーザーの削除リンクが得られます。

- 11. リクエストのエンドポイントを次のように変更すると一般ユーザーが削除できます。

```
GET /admin/delete?username=carlos HTTP/2
```