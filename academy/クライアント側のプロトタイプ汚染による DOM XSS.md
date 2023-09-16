# クライアント側のプロトタイプ汚染による DOM XSS

次のハッキングラボを実施します  
https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-dom-xss-via-client-side-prototype-pollution  
このラボは、クライアント側のプロトタイプ汚染による DOM XSS に対して脆弱です。

[前準備]  
汚染源を手動で見つけるのは大変なので、ツールを使用します

- 1. Burp Suite 組み込みのブラウザを立ち上げます
- 2. 右上の拡張機能ボタンをクリックして、Burp Suite をピン止めします
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/4447df55-eb96-45ab-82b9-5701e89d810d)

* 3. BurpSuite 拡張機能をクリックして DOM Invader を有効にします
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/f0db906a-cc4d-4ffe-8b57-566a257c90d0)

[ハッキング手順]

- 1. Burp Suite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. DOM Invader の設定を Prototype pollution is on にします
- 4. F12 キーを押して、Devtool を起動し、DOM Invator タブを開きます

* 5. Scan for gudgets を押下します
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/74e05d70-775e-4ecf-a1bc-422d1b1d4374)

* 6. 脆弱性が見つかると、以下のように表示されるので Exploit ボタンを押下します。
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/afef9148-e1db-49ad-978f-423f848c9fd4)

alert(1)が実行され、任意のスクリプトが実行できることが確認できました。
