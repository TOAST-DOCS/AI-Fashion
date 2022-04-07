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
### 필터링 가이드

* 제한된 카테고리로 검색할 때 사용합니다.

#### 대상 필드
| 이름 | 필드명 |
| --- | --- |
| 카테고리 1depth | category1_id |
| 카테고리 2depth | category2_id |
| 카테고리 3depth | category3_id |

#### 문법
* `filter.{필드 명}`=`연산자`:`값`
* 값(values)은 양의 정수만 가능합니다. 타입 (uint32: 0~4294967295)

| 조건 | 연산자 | 예제 | 설명 |
| --- | --- | --- | --- |
| equal(default) | equal | filter.category2_id=1003,1005 or<br/>filter.category2_id=equal:1003 | 대상 필드의 값이 파라미터 값과 같은 문서만 결과로 응답합니다.<br/>콤마(,)로 구분하여 or 검색이 가능합니다. |
| not equal | !equal | filter.category2_id=!equal:1003 or <br/>filter.category2_id=!equal:1003,1005 | 대상 필드의 값이 파라미터 값과 다른 문서만 결과로 응답합니다.<br/>콤마(,)로 구분하여 or 검색이 가능합니다. |
| with in range | range | filter.category2_id=range:1003:1004 | 대상 필드의 값이 파라미터 값의 범위 내에 있는 문서만 결과로 응답합니다. |
| out of range | !range | filter.category2_id=!range:1002:1004 | 대상 필드의 값이 파라미터 값의 범위 밖에 있는 문서만 결과로 응답합니다. |

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
| filter.category1_id | string | X | equal:3 | category1_id 값을 필터링 |
| filter.category2_id | string | X | !equal:3 | category2_id 값을 필터링 |
| filter.category3_id | string | X | range:1003:1005 | category3_id 값을 필터링 |

* filter.category1~3_id는 [필터링 가이드](./service-api-guide/#filtering-guide)에서 확인 가능

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
| filter.category1_id | string | X | equal:3 | category1_id 값을 필터링 |
| filter.category2_id | string | X | !equal:3 | category2_id 값을 필터링 |
| filter.category3_id | string | X | range:1003:1005 | category3_id 값을 필터링 |

* filter.category1_id~3은 [필터링 가이드](./service-api-guide/#filtering-guide)에서 확인 가능

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image?limit=100&link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0%3D"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./service-api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | 検索結果の総数 |
| data.query | String | O | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | 検索クエリ |
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
        "query": "link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWfnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUiOiJBTEwiLpbnB1dHMi0lt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MyLCJ3aWa0aCI6MTU1LCJozWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0=&limit=100",
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

##### レスポンスコード

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

## ディープタギング

<span id="tag-api"></span>
### Tag

* 入力画像からファッションアイテムのタグ情報を検出するAPIです。

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| serviceID | サービスID |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| path | String | O | `https://imagecdn.co.kr/sample_image.jpg` | URL Encodeされた画像URL |
| lang | String | X | ko | labelの言語<br/>default: en<br/>en: English<br/>ko: Korean |
| item_limit | int | X | 3 | 画像で見つかったファッションアイテムのうち、タグ情報を返すアイテムの数<br/>アイテムの幅が長い順にソート<br/>default：1<br/>最大サイズ<br>1以上4以下に設定可能 |

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=en&item_limit=3"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./service-api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 2 | 検索結果の総数 |
| data.query | String | O | `path=https://imagecdn.co.kr/sample_image.jpg&lang=ko&item_limit=3` | 検索クエリ |
| data.items[].type | String | O | JACKET | 検出されたアイテムのtype |
| data.items[].score | float32 | O | 0.9515 | 検出されたアイテムの信頼度 |
| data.items[].tags | Array of json object | O |  | 検出されたアイテムタグ情報の配列 |
| data.items[].tags[].attribute | String | O | category | タグのプロパティ |
| data.items[].tags[].labels | Array of json object | O |  | タグラベルの配列 |
| data.items[].tags[].labels[].label | String | O | ブラウス\| Blouse | タグラベル<br/>URL Parameterのlangによりレスポンス言語が異なる |
| data.items[].tags[].labels[].score | float32 | O | 0.9545 | タグラベルの信頼度 |
| data.items[].center | float64 array | O | [0.825047801147227, 0.330948979591837] | 検出されたアイテムの中央x、y座標% |
| data.items[].b0 | float64 array | O | [0.676864247418738, 0.219377551020408] | 検出されたアイテムのx0、y0座標% |
| data.items[].b1 | float64 array | O | [0.973231355525813, 0.4426204081632654] | 検出されたアイテムのx1、y1座標% |

<br>
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
        "query": "path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=en&item_limit=3",
        "items": [{
            "type": "SHIRT",
            "tags": [{
                "attribute": "category", "labels": [{ "label": "Blouse", "score": 0.9966272115707397 }]
            }, {
                "attribute": "color", "labels": [{ "label": "Beige", "score": 0.7692235112190247 }]
            }, {
                "attribute": "pattern", "labels": [{ "label": "Solid", "score": 0.9893960356712341 }]
            }, {
                "attribute": "fabric", "labels": [{ "label": "Silk", "score": 0.586938738822937 }]
            }, {
                "attribute": "neckline", "labels": [{ "label": "ShirtCollar", "score": 0.9922573566436768 }]
            }, {
                "attribute": "shoulder", "labels": [{ "label": "PuffShoulder", "score": 0.5369117856025696 }]
            }, {
                "attribute": "sleeve_length", "labels": [{ "label": "LongSleeve", "score": 0.6998409032821655 }]
            }, {
                "attribute": "sleeve_shape", "labels": [{ "label": "Straight", "score": 0.689109206199646 }]
            }, {
                "attribute": "length_up", "labels": [{ "label": "Waist", "score": 0.9575495719909668 }]
            }, {
                "attribute": "age", "labels": [{ "label": "Adult", "score": 0.9985153079032898 }]
            }, {
                "attribute": "gender", "labels": [{ "label": "Female", "score": 0.9960111379623413 }]
            }, {
                "attribute": "detail", "labels": [{ "label": "Buttoned", "score": 0.9440848231315613 }]
            }, {
                "attribute": "fit", "labels": [{ "label": "Normal/Regular", "score": 0.789472222328186 }]
            }],
            "center": [ 0.46125, 0.34125 ],
            "b0": [ 0.1875, 0.0175 ],
            "b1": [ 0.735, 0.665 ],
            "score": 0.93118
        }, {
            "type": "SKIRT",
            "tags": [{
                "attribute": "category", "labels": [{ "label": "Skirt", "score": 0.9997897744178772 }]
            }, {
                "attribute": "color", "labels": [{ "label": "Brown", "score": 0.8597127199172974 }]
            }, {
                "attribute": "pattern", "labels": [{ "label": "Solid", "score": 0.988312304019928 }]
            }, {
                "attribute": "fabric", "labels": [{ "label": "Canvas", "score": 0.24775846302509308 }]
            }, {
                "attribute": "length_lo", "labels": [{ "label": "Short/Thigh", "score": 0.9987099170684814 }]
            }, {
                "attribute": "age", "labels": [{ "label": "Adult", "score": 0.9993846416473389 }]
            }, {
                "attribute": "gender", "labels": [{ "label": "Female", "score": 0.9950520396232605 }]
            }, {
                "attribute": "detail", "labels": [{ "label": "WrapStyle", "score": 0.7058117985725403 }]
            }, {
                "attribute": "fit", "labels": [{ "label": "Normal/Regular", "score": 0.9844645857810974 }]
            }, {
                "attribute": "shape", "labels": [{ "label": "A-line", "score": 0.9432026743888855 }]
            }],
            "center": [ 0.5, 0.69125 ],
            "b0": [ 0.28, 0.445 ],
            "b1": [ 0.72, 0.9375 ],
            "score": 0.939945
        }]
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
| -45020 | ImageTooLargeException | 画像ファイルのサイズが大きすぎる<br>[入力画像ガイド](./service-api-guide/#input-image-guide)参考 |
| -45040 | InvalidImageFormatException | サポートしない画像ファイル形式<br>[入力画像ガイド](./service-api-guide/#input-image-guide)参考 |
| -45050 | InvalidImageURLException | アクセスできないURL |
| -45060 | ImageTimeoutError | 画像ダウンロード時間超過 |
| -50000 | InternalServerError | サーバーエラー |
