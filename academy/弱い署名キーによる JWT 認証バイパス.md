# 弱い署名キーによる JWT 認証バイパス

次のハッキングラボを実施します  
https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-weak-signing-key  
このラボでは、セッションの処理に JWT ベースのメカニズムを使用します。トークンの署名と検証の両方に非常に弱い秘密鍵を使用します。

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
Content-Length: 2602
・・・
Admin interface only available if logged in as an administrator
```

- 7.JWT をコピーし、シークレットをブルート フォースで攻撃します。次のように hashcat を使用してこれを行うことができます。

```
hashcat -a 0 -m 16500 <YOUR-JWT> /path/to/jwt.secrets.list
```

※[攻撃リスト](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list)

- 8. secret が「secret1」であることが分かります

- 9. secret1 を base64 エンコーディングします

```
c2VjcmV0MQ==
```

- 10. JWT Editer key タブを選択し、New Symmetric key を押下します、k を上記のエンコーディング値に置き換えて OK を押します。  
      これで偽造された認証キーが作成できます。

![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/715677a9-a76a-48a9-b36b-ba0fff979fb0)

- 11. repeater で/admin ページが利用できるようになっていることが確認できます

- 12. エンドポイントを/admin/delete?username=carlos に変更して送信すると一般ユーザーが削除できます
