# ストアード XSS を href 属性に埋め込む

次のハッキングラボを実施します  
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-href-attribute-double-quotes-html-encoded

- 1. ハッキングラボにアクセスします
- 2. ブログ記事のコメント欄全ての項目に任意の文字列を入力して、コメントを投稿します
- 3. コメント一覧が表示されているページでソースを確認します

```html
<section class="comment">
  <p><img src="/resources/images/avatarDefault.svg" class="avatar" /> <a id="author" href="abc">abc</a> | 07 August 2023</p>
  <p>abc</p>
  <p></p>
</section>
```

web サイトリンクが href 属性に格納されています

- 4. もう一度コメントを書き込みます。ただし web サイトの項目をスクリプトにします

```
javascript:alert(1)
```

これにより、コメント欄の web サイトリンクをクリックするとスクリプトが実行されることが確認できます
