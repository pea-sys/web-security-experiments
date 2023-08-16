# OWASP ZAP でコメントの残弱性を検出

以下のハッキングラボの Web ページにはコメントが残されており、そこにセキュリティホールがあります。  
https://portswigger.net/academy/labs/launch/18ebd0ac7489cd1770d58baec6ec290f6d15c7346d25a11f0e64cc1ad3329348?referrer=%2fweb-security%2finformation-disclosure%2fexploiting%2flab-infoleak-on-debug-page

コメントの検出は Burp Suite でも可能ですが、有料版じゃないと使えない機能なので今回は OWASP ZAP を使用します。

- 1. OWASP ZAP を起動し、ツールタブからスパイダー検索を選択
     ![1](https://github.com/pea-sys/Til/assets/49807271/6c6c1950-d8e2-42b1-9729-7698cb9236a5)

- 2. 攻撃対象サイトの URL をペーストしてスキャンを開始
     ![2](https://github.com/pea-sys/Til/assets/49807271/b8cf35a2-8b99-4fef-96e2-9b7834bd7a14)

- 3. アラートを表示して、Information Disclosure -Suspicious Comments を選択
     ![3](https://github.com/pea-sys/Til/assets/49807271/918f24bc-5b04-46ca-858e-1d7e055372d4)

- 4. アラートの中身を確認すると、/cgi-bin/phpinfo.php リンクのコメントが確認できます
     ![4](https://github.com/pea-sys/Til/assets/49807271/6d5ac493-2fcb-43b4-871b-4fea98332fde)

* 5.Burp Suite を起動し、ブラウザを起動後、ハッキングラボにアクセス

* 6.ハッキングラボ/phpinfo.php にアクセス後、Burp Suite でレスポンスをチェックすると、一般ユーザーに参照されてはいけない情報が確認できます(SECRET_KEY 等)
  ![5](https://github.com/pea-sys/Til/assets/49807271/85623d07-8dee-47b4-8f70-62828cde10ed)
