# Group 認可

## トークン [/oauth/token]

oauthのAPIを叩いた後、access_tokenの値をアプリ側で保存してください。  
で、保存した値はAPIアクセスの際に利用します。  
X-HOGE-Authorizationヘッダに保存したaccess_tokenの値を指定してください。  

### トークン取得 [POST]

### scope

value            | description                                          |
----------------:| ---------------------------------------------------- |
resources        | リソースオーナーの所有するリソースへの全アクセス許可 |
session_exchange | Session Exchange機能の利用許可                       |

#### Session Exchange機能

リクエストヘッダー`X-HOGE-ACCESS-TOKEN`に`access_token`の値をセットしてリクエストを行うとセッションがログイン状態になる機能。

+ Request (application/json)
    + Headers

            Accept: application/json

    + Attributes
        + email: test@example.com (string, required) - メールアドレス（format: email）
        + password: abc123 (string, required) - パスワード（pattern: ^[0-9A-Za-z]{6,16}$）

+ Response 200 (application/json)

    + Body

            {
                "access_token": "hogehoge...",
                "token_type": "bearer",
                "expires_in": 7200,
                "refresh_token": "e5c0f55b...",
                "scope": "resources",
                "created_at": 1459145622
            }

+ Response 400 (application/json)

    + Body

            {
                "error": true,
                "message": "ユーザー名またはパスワードに誤りがあります"
            }

+ Response 404 (application/json)

    + Body

            {
                "error": true,
                "message": "ユーザー名またはパスワードに誤りがあります"
            }
