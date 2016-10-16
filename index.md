FORMAT: 1A
HOST: http://localhost:8000/api/v1

# HOGEアプリ API仕様書

本仕様書は API Bluepoint の使い方の説明のために作成されました。  
いろいろアレですがざっくりと説明できりゃいいのです。


## 基本仕様

たとえばAPIの基本仕様をツラツラと書いたり。

ステータスコード | 意味     | レスポンス |
--------------| ------- | -------- |
200           | OK      | JSON       |
201           | created | JSON       |
204           | No Content | nil |
400           | Bad Request | エラーメッセージ欄参照 |
401           | Unauthorized | 認証エラー |
403           | Forbedden | エラーメッセージ欄参照 |
404           | Not Found | JSON |
500           | Internal Server Error | nil |
503           | Service Unavailable | nil |


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

# Group SNS機能

## SNSフィード [/communities/{community_id}/posts]

+ Parameters

    + community_id: `1` (required, number/string) - 表示するコミュニティのID（全ての場合は`all`）

### 投稿一覧を取得 [GET]

SNSフィードのタイムラインに表示する投稿をコミュニティ別に取得します。コミュニティに関わりなく全ての投稿を取得する場合は`community_id`に`all`を指定します。

### クエリパラメータ（オプション）
key      | type    | value | description |
--------:| ------- | ----- | ----------- |
page     | integer | 1     | データ取得位置|
per_page | integer | 1     | データ取得量 |


+ Response 200 (application/json)

        [
            {
                "id": 2,
                "text": "美味しそう！青いウニとか、海田さんマジパネエッす！",
                "user_id": 2,
                "user":                 {
                     "id": 2,
                     "name": "山田 山彦",
                     "avatar": "https://localhost:3000/images/producers/3.jpg",
                     "type": "Consumer",
                     "roles": ["consumer"]
                 },
                "community_id": "1",
                "is_liked": true,
                "like_count": 1,
                "comment_count": 1,
                "picture": "https://localhost:3000/images/articles/3.jpg",
                "created_at": "2015-08-05T08:40:51.620Z",
                "updated_at": "2015-08-05T08:40:51.620Z"
            },
            {
                "id": 1,
                "text": "世界初！青いウニ！今年のウニは一味どころか見た目も全然違います。青い海で採れた、青いウニ！",
                "user_id": 1,
                "user":                 {
                     "id": 2,
                     "name": "海田 海男",
                     "avatar": "https://localhost:3000/images/producers/2.jpg",
                     "display_address": "山形県山形市",
                     "type": "Producer",
                     "roles": ["consumer", "producer"]
                 },
                "community_id": 1,
                "is_liked": false,
                "like_count": 1,
                "comment_count": 1,
                "picture": "https://localhost:3000/images/articles/4.jpg",
                "created_at": "2015-08-05T08:40:51.620Z",
                "updated_at": "2015-08-05T08:40:51.620Z"
            }
        ]

### 投稿を新規作成 [POST]

タイムラインに投稿します。community_idは投稿先です。

+ Request (application/json)

        {
            "text": "究極の味噌漬け豚肉。岩手名産、今年最初の出荷です",
            "picture": {
                "data": "BASE64_ENCODED_IMAGE_DATA",
                "filename": "image.png",
                "content_type": "image/png"
            },
            "facebook": "ユーザもしくはページのアクセストークン",
            "twitter": "アクセストークン",
            "notify": true
        }

+ Response 201 (application/json)

    + Headers

            Location: /posts/3

+ Response 400 (application/json)

    + Body

                {
                    "error": true,
                    "message": "バリデーション関連のエラーメッセージ"
                }


## SNSフィード投稿 [/posts/{post_id}]

投稿の詳細

+ Parameters

    + post_id: `1` (required, number) - 投稿のID

### 投稿を表示 [GET]

投稿の詳細を取得します。

+ Response 200 (application/json)

        {
            "id": 3,
            "text": "究極の味噌漬け豚肉。岩手名産、今年最初の出荷です",
            "user_id": 1,
            "user":                 {
                     "id": 1,
                     "name": "山本　郁男",
                     "avatar": "https://localhost:3000/images/producers/1.jpg",
                     "display_address": "新潟県上越市",
                     "type": "Producer",
                     "roles": ["consumer", "producer"]
                 },
            "community_id": 1,
            "is_liked": true,
            "like_count": 1,
            "comment_count": 1,
            "image": "https://localhost:3000/images/articles/11.jpg",
            "created_at": "2015-08-05T08:40:51.620Z",
            "updated_at": "2015-08-05T08:40:51.620Z"
        }

+ Response 404 (application/json)

    + Body

                {
                    "error": true,
                    "message": "エラーメッセージ"
                }



### 投稿を更新 [PUT]

投稿を更新します

::: warning
自分の投稿しか編集することはできません。
:::

+ Request (application/json)

    + Body

            {
                "text": "究極の味噌漬け豚肉。岩手名産、今年最初の出荷です",
                "picture":             {
                 "data": "BASE64_ENCODED_IMAGE_DATA",
                 "filename": "image.png",
                 "content_type": "image/png"
             },
                "facebook": "ユーザもしくはページのアクセストークン",
                "twitter": "アクセストークン",
                "notify": true
            }

+ Response 200 (application/json)

    + Headers

            Location: /posts/1

+ Response 400 (application/json)

    + Body

                {
                    "error": true,
                    "message": "バリデーション関連のエラーメッセージ"
                }


+ Response 404 (application/json)

    + Body

                {
                    "error": true,
                    "message": "エラーメッセージ"
                }


### 投稿を削除 [DELETE]

投稿を削除します。

::: warning
自分の投稿しか編集することはできません。
:::

+ Response 204

+ Response 404

    + Body

                {
                    "error": true,
                    "message": "エラーメッセージ"
                }


## SNSフィードいいね [/posts/{post_id}/like]

投稿に「いいね」します。

+ Parameters
    + post_id: `1` (required, number) - 投稿のID

### いいねを投稿 [POST]

+ Response 201
+ Response 404

    + Body

                {
                    "error": true,
                    "message": "エラーメッセージ"
                }


### いいねを取り消す [DELETE]

+ Response 204
+ Response 404
    + Body

                {
                    "error": true,
                    "message": "エラーメッセージ"
                }


## 投稿へのコメント [/posts/{post_id}/comments]

コメントの一覧

+ Parameters
    + post_id: `1` (required, number) - 投稿のID

### コメントを取得 [GET]

投稿へのコメントを取得します。

### クエリパラメータ（オプション）
key      | type    | value | description |
--------:| ------- | ----- | ----------- |
since_id | integer | 1     | データ取得位置|


+ Response 200 (application/json)

        [
            {
                "id": 2,
                "text": "青いウニとか、マジパネエッす！",
                "user_id": 2,
                "user":                 {
                     "id": 3,
                     "name": "海田 海男",
                     "avatar": "https://localhost:3000/images/producers/4.jpg",
                     "type": "Consumer",
                     "roles": ["consumer"]
                 },
                "post_id": 1,
                "created_at": "2015-08-05T08:40:51.620Z",
                "updated_at": "2015-08-05T08:40:51.620Z"
            },
            {
                "id": 1,
                "text": "今年のウニは一味どころか見た目も全然違います。青い海で採れた、青いウニ！",
                "user_id": 1,
                "user":                 {
                     "id": 1,
                     "name": "山本　郁男",
                     "avatar": "https://localhost:3000/images/producers/1.jpg",
                     "display_address": "新潟県上越市",
                     "type": "Producer",
                     "roles": ["consumer", "producer"]
                 },
                "post_id": 1,
                "created_at": "2015-08-05T08:40:51.620Z",
                "updated_at": "2015-08-05T08:40:51.620Z"
            }
        ]

### コメントを投稿 [POST]

コメントを投稿します。

+ Request (application/json)

        {
            "text": "最高のコメントです"
        }

+ Response 201 (application/json)

    + Headers

            Location: /comments/{comment_id}

+ Response 400 (application/json)
    + Body

                {
                    "error": true,
                    "message": "バリデーション関連のエラーメッセージ"
                }


## コメント [/comments/{comment_id}]

+ Parameters

    + comment_id: `1` (required, number) - コメントのID


### コメントを削除 [DELETE]

コメントを削除します。

+ Response 204

+ Response 404

    + Body

                {
                    "error": true,
                    "message": "エラーメッセージ"
                }


