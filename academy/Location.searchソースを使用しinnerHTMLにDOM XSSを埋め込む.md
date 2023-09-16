# Location.search ソースを使用し innerHTML に DOM XSS を埋め込む

以下のハッキングラボを実施します  
https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink

- 2. ハッキングラボにアクセスします
- 3. 以下のワードで検索します

```html
<img src="1" onerror="alert(1)" />
```

画像参照エラーによる例外処理によりスクリプト実行が確認できます  
※回答を見て感心したけど、どうやって気づけば良いか難しい
