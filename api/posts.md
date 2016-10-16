# Group SNS機能

## SNSフィード [/communities/{community_id}/posts]

+ Parameters

    + community_id: `1` (required, number/string) - 表示するコミュニティのID（全ての場合は`all`）

### 投稿一覧を取得 [GET]

SNSフィードのタイムラインに表示する投稿をコミュニティ別に取得します。コミュニティに関わりなく全ての投稿を取得する場合は`community_id`に`all`を指定します。

<!-- include(data/params.md) -->

+ Response 200 (application/json)

        [
            {
                "id": 2,
                "text": "美味しそう！青いウニとか、海田さんマジパネエッす！",
                "user_id": 2,
                "user": <!-- include(data/user_data1.md) -->,
                "community_id": "1",
                "is_liked": true,
                "like_count": 1,
                "comment_count": 1,
                "picture": "https://localhost:3001/images/articles/3.jpg",
                "created_at": "2015-08-05T08:40:51.620Z",
                "updated_at": "2015-08-05T08:40:51.620Z"
            },
            {
                "id": 1,
                "text": "世界初！青いウニ！今年のウニは一味どころか見た目も全然違います。青い海で採れた、青いウニ！",
                "user_id": 1,
                "user": <!-- include(data/producer_data2.md) -->,
                "community_id": 1,
                "is_liked": false,
                "like_count": 1,
                "comment_count": 1,
                "picture": "https://localhost:3001/images/articles/4.jpg",
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
        <!-- include(data/invalid.md) -->

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
            "user": <!-- include(data/producer_data1.md) -->,
            "community_id": 1,
            "is_liked": true,
            "like_count": 1,
            "comment_count": 1,
            "image": "https://localhost:3001/images/articles/11.jpg",
            "created_at": "2015-08-05T08:40:51.620Z",
            "updated_at": "2015-08-05T08:40:51.620Z"
        }

+ Response 404 (application/json)

    + Body
        <!-- include(data/error.md) -->


### 投稿を更新 [PUT]

投稿を更新します

::: warning
自分の投稿しか編集することはできません。
:::

+ Request (application/json)

    + Body

            {
                "text": "究極の味噌漬け豚肉。岩手名産、今年最初の出荷です",
                "picture": <!-- include(data/image.md) -->,
                "facebook": "ユーザもしくはページのアクセストークン",
                "twitter": "アクセストークン",
                "notify": true
            }

+ Response 200 (application/json)

    + Headers

            Location: /posts/1

+ Response 400 (application/json)

    + Body
        <!-- include(data/invalid.md) -->

+ Response 404 (application/json)

    + Body
        <!-- include(data/error.md) -->

### 投稿を削除 [DELETE]

投稿を削除します。

::: warning
自分の投稿しか編集することはできません。
:::

+ Response 204

+ Response 404

    + Body
        <!-- include(data/error.md) -->

## SNSフィードいいね [/posts/{post_id}/like]

投稿に「いいね」します。

+ Parameters
    + post_id: `1` (required, number) - 投稿のID

### いいねを投稿 [POST]

+ Response 201
+ Response 404

    + Body
        <!-- include(data/error.md) -->

### いいねを取り消す [DELETE]

+ Response 204
+ Response 404
    + Body
        <!-- include(data/error.md) -->

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
                "user": <!-- include(data/user_data2.md) -->,
                "post_id": 1,
                "created_at": "2015-08-05T08:40:51.620Z",
                "updated_at": "2015-08-05T08:40:51.620Z"
            },
            {
                "id": 1,
                "text": "今年のウニは一味どころか見た目も全然違います。青い海で採れた、青いウニ！",
                "user_id": 1,
                "user": <!-- include(data/producer_data1.md) -->,
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
        <!-- include(data/invalid.md) -->

## コメント [/comments/{comment_id}]

+ Parameters

    + comment_id: `1` (required, number) - コメントのID


### コメントを削除 [DELETE]

コメントを削除します。

+ Response 204

+ Response 404

    + Body
        <!-- include(data/error.md) -->
