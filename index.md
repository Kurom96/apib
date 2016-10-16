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


<!-- include(api/oauth.md) -->
<!-- include(api/posts.md) -->
