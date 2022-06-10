## AI Service > AI Fashion > Service APIガイド

- AI Fashion検索サービスを使用するために必要なAPIを説明します。

## API共通情報
### 事前準備
- APIを使用するにはプロジェクト統合AppkeyまたはサービスAppkeyが必要です。
    - プロジェクト統合Appkeyを使用することを推奨します。
    - プロジェクト統合Appkeyはプロジェクト設定ページのAPIセキュリティ設定で作成して使用できます。
    * サービスAppkeyはコンソール上部の**URL & Appkey**メニューで確認が可能です。

### リクエスト共通情報
- APIを使用するにはセキュリティキー認証処理が必要です。

[APIドメイン]

| リージョン | ドメイン |
| --- | --- |
| 韓国(パンギョ) | https://kr1-aifashion.api.nhncloudservice.com |
| 韓国(ピョンチョン) | https://kr2-aifashion.api.nhncloudservice.com |
| 日本(東京) | https://jp1-aifashion.api.nhncloudservice.com |

<span id="input-image-guide"></span>
### 入力画像ガイド

* 入力画像のダウンロードに3秒以上かかる場合は失敗とみなします。
* 入力イ画像ファッションアイテムの幅と高さがどちらも20px以下の場合は認識しません。
* 画像サイズの大きさに比例してファッションアイテムのサイズも大きくすると正確に認識できます。
* 画像内でファッションアイテムが占める割合が大きいほど正確に認識できます。
* 画像ファイルの最大サイズ：5,000,000 bytes(ただし、[tag API](#tag-api)は1,000,000 bytes)
* サポート画像フォーマット：PNG、JPEG、GIF

<span id="filtering-guide"></span>
### フィルタリングガイド

* 制限されたカテゴリーで検索するときに使用します。

#### 対象フィールド
| 名前 | フィールド名 |
| --- | --- |
| カテゴリー1depth | category1_id |
| カテゴリー2depth | category2_id |
| カテゴリー3depth | category3_id |

#### 文法
* `filter.{フィールド名}`=`演算子`:`値`

| 条件 | 演算子 | 例 | 説明 |
| --- | --- | --- | --- |
| equal(default) | equal | filter.category2_id=1003,1005 or<br/>filter.category2_id=equal:1003 | 対象フィールドの値がパラメータ値と同じ文書のみ結果としてレスポンスします。<br/>コンマ(,)で区切ってOR検索が可能です。 |
| not equal | !equal | filter.category2_id=!equal:1003 or <br/>filter.category2_id=!equal:1003,1005 | 対象フィールドの値がパラメータ値と異なる文書のみ結果としてレスポンスします。<br/>コンマ(,)で区切ってOR検索が可能です。 |

<span id="common-response"></span>
### レスポンス共通情報

- すべてのAPIリクエストに「200 OK」でレスポンスします。詳細なレスポンス結果はレスポンス本文ヘッダを参照します。

[レスポンス本文ヘッダ]

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| header.isSuccessful | boolean | true：正常<br>false：エラー |
| header.resultCode | int | 0：正常<br>0より大きい：部分成功<br>負の数：エラー |
| header.resultMessage | string | "SUCCESS"：正常<br>その他：エラーメッセージを返す |

[成功レスポンス本文例]

```json
{
        "header": {
                "isSuccessful": true,
                "resultCode": 0,
                "resultMessage": "SUCCESS"
        }
}
```

[失敗レスポンス本文例]

```json
{
        "header": {
                "isSuccessful": false,
                "resultCode": -40000,
                "resultMessage": "InvalidParam"
        }
}
```

## サービス監理

### サービスID登録

* サービスIDを登録することができるAPI
* 最大5個まで登録可能
* 許可文字
    * 英字小文字、数字、(-)、(_)許可
    * 2～32文字まで許可
    * 最初の文字は英字小文字のみ許可

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| POST | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| serviceID | サービスID |

<details><summary>リクエスト例</summary>

```
curl -X POST "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./service-api-guide/#common-response)で確認可能

<details><summary>レスポンス本文例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000 | InvalidParam | パラメータにエラーがある |
| -41000 | UnauthorizedAppKey | 承認されていないAppkey |
| -42010 | DuplicateServiceID | 重複するサービスID |
| -42030 | ServiceIDQuotaExceededException | 許可されたサービスID数超過 |
| -50000 | InternalServerError | サーバーエラー |

### サービスID削除

* 登録されたサービスIDを削除することができるAPI
* サービスIDに処理中のインデックスリクエストがある場合は、サービスIDを削除できません。

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| DELETE | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| serviceID | サービスID |

<details><summary>リクエスト例</summary>

```
curl -X DELETE "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./service-api-guide/#common-response)で確認可能

<details><summary>レスポンス本文例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000 | InvalidParam | パラメータにエラーがある |
| -41000 | UnauthorizedAppKey | 承認されていないAppkey |
| -42000 | NotExistServiceID | 登録されていないサービスID |
| -42020 | CannotDeletedOnIndexRunning | インデックス中は削除できません。 |
| -50000 | InternalServerError | サーバーエラー |

### サービスIDリスト

* 登録されたサービスIDリストを確認することができるAPI

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/services |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/services"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./service-api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 5 | 検索結果の総数 |
| data.items[].serviceID | String | O | my-service | サービスID |

<details><summary>レスポンス本文例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 2,
        "items": [{
                "serviceID": "my-service"
            },
            {
                "serviceID": "second_service"
            }
        ]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000 | InvalidParam | パラメータにエラーがある |
| -41000 | UnauthorizedAppKey | 承認されていないAppkey |
| -50000 | InternalServerError | サーバーエラー |

## 類似画像商品推薦

### Search By ProductID

* 商品IDを基に類似したファッションアイテムを含む商品を探すAPI

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID} |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| serviceID | サービスID |
| productID | 商品ID |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | 最大サイズ<br>1以上200以下で設定可能 |
| filter.category1_id | string | X | equal:3 | category1_id値でフィルタリング |
| filter.category2_id | string | X | !equal:3 | category2_id値でフィルタリング |
| filter.category3_id | string | X | !equal:3 | category3_id値でフィルタリング |
| threshold | float32 | X | 0.8 | マッチングしたかどうかを判断する類似度基準値<br/> data.items[].similarity >= thresholdの項目のみマッチングしたと判断します。<br/>0超過1.0以下に設定可能 |

* filter.category1~3_idは[フィルタリングガイド](./service-api-guide/#filtering-guide)で確認可能

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID}?limit=100"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./service-api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | 検索結果の総数 |
| data.query | String | O | productID=10234455&limit=100 | 検索クエリ |
| data.items[].similarity | Number | O | 0.91234 | 検索類似度スコア |
| data.items[].productID | String | O | 8980335 | 商品ID |

<details><summary>レスポンス本文例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 100,
        "query": "productID=10234455&limit=100",
        "items": [{
                "similarity": 0.91234,
                "productID": "8980335"
            },
            {
                "similarity": 0.81234,
                "productID": "7980335"
            }
        ]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000 | InvalidParam | パラメータにエラーがある |
| -40050 | NotFoundProductIDError | 商品IDがない |
| -41000 | UnauthorizedAppKey | 承認されていないAppkey |
| -42000 | NotExistServiceID | 登録されていないサービスID |
| -50000 | InternalServerError | サーバーエラー |

## カメラ検索

### Detect

* 入力画像からファッションアイテムを検出するAPIです。

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| serviceID | サービスID |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| path | String | O | `https://imagecdn.co.kr/sample_image.jpg` | URL Encodingされた画像のurl |

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./service-api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | 検索結果の総数 |
| data.query | String | O | `path=https://imagecdn.co.kr/sample_image.jpg` | 検索クエリ |
| data.items[].link | String | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | search by imageで使用するリンク |
| data.items[].center | float64 array | O | [0.825047801147227, 0.330948979591837] | 検出されたアイテムの中央x、y座標% |
| data.items[].b0 | float64 array | O | [0.676864247418738, 0.219377551020408] | 検出されたアイテムのx0、y0座標% |
| data.items[].b1 | float64 array | O | [0.973231355525813, 0.4426204081632654] | 検出されたアイテムのx1、y1座標% |
| data.items[].score | float32 | O | 0.9732 | 検出されたアイテムの信頼度 |

<details><summary>レスポンス本文例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 2,
        "query": "path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg",
        "items": [{
                "link": "eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWFnZS1zZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3VjLm9jdGV0LXN0cmVbsInR5cGUiOiJBTEwiLCJpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MTcyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVfV0sImNvbmZpZ3MiOnsiY2FtZXJjp0cnVlfX0=",
                "center": [0.825047801172275, 0.330998979591837],
                "b0": [0.676864244718738, 0.219387751020408],
                "b1": [0.973231357555813, 0.4426020401632654],
                "score": 0.97323
            },
            {

                "link": "eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUiOiJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0=",
                "center": [0.3929254301032506, 0.572066265306123],
                "b0": [0.3288718929253023, 0.506377551204082],
                "b1": [0.456978967952199, 0.637751020408163],
                "score": 0.97123

            }
        ]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000 | InvalidParam | パラメータにエラーがある |
| -41000 | UnauthorizedAppKey | 承認されていないAppkey |
| -42000 | NotExistServiceID | 登録されていないサービスID |
| -45020 | ImageTooLargeException | 画像ファイルが大きすぎる<br>[入力画像ガイド](./service-api-guide/#input-image-guide)参考 |
| -45040 | InvalidImageFormatException | サポートしていない画像形式<br>[入力画像ガイド](./service-api-guide/#input-image-guide)参考 |
| -45050 | InvalidImageURLException | アクセスできないURL |
| -45060 | ImageTimeoutError | 画像ダウンロード時間超過 |
| -50000 | InternalServerError | サーバーエラー |

### Search By Image

* detect apiから返されたlinkを基に類似したファッションアイテムを含む商品を探すAPI

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| serviceID | サービスID |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | 最大サイズ<br>1以上200以下で設定可能 |
| link | string | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0%3D | detect APIから伝達されたlink(URL encodingが必要) |
| filter.category1_id | string | X | equal:3 | category1_id値でフィルタリング |
| filter.category2_id | string | X | !equal:3 | category2_id値でフィルタリング |
| filter.category3_id | string | X | !equal:3 | category3_id値でフィルタリング |
| threshold | float32 | X | 0.8 | マッチングしたかどうかを判断する類似度基準値<br/> data.items[].similarity >= thresholdの項目のみマッチングしたと判断します。<br/>0超過1.0以下に設定可能 |
