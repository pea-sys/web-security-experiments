# Burp Suite で OAuth 暗黙的フローによる認証バイパス

OAuth 暗黙的フローは全ての通信をブラウザのリダイレクトを介して行うため、安全性が低いです。

- 1.OAuth 認証を行います（メールアドレスとトークンが紐づいてることとします）
  この際には、クライアント側からメールアドレスを送信することになります。

```
{"email":"wiener@hotdog.com","username":"wiener","token":"M-yQOXQlGq4CTITMpr_DTgpSaP6niEx7boJcs-5EzhB"}
```

- 2.Burp Suite を起動し、上記 POST リクエストを選択し、Send to Repeater します。
  ![1](https://github.com/pea-sys/Til/assets/49807271/f0dcf86f-09f0-4c36-9363-43c8ce500125)

* 3. POST リクエストのメールアドレスを攻撃対象のアドレスに変更して、送信します

* 4. Response が返ってきたら、右クリックして Request in browser -> in original session を選択しして、表示される URL をブラウザに張り付けます。
     ![2](https://github.com/pea-sys/Til/assets/49807271/1afa9467-96aa-4e39-9c52-7b288d80fcf9)

* 5.攻撃対象のメールアドレスに紐づけられたアカウントでログイン出来ていることを確認します
