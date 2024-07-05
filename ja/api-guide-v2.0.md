## AI Service > AI Fashion > API v2.0ガイド

- AI Fashion v2.0サービスを利用するために必要なAPIを説明します。

## API共通情報
### 事前準備
- APIを使用するためには、プロジェクト統合アプリキーまたはサービスアプリキーが必要です。
    - プロジェクト統合アプリキーの使用を推奨します。
    - プロジェクト統合アプリキーは、プロジェクト設定ページのAPIセキュリティ設定で生成して使用できます。
    - サービスアプリキーは、コンソール上部の**URL & Appkey** メニューで確認できます。

### リクエスト共通情報
- APIを使用するためには、セキュリティキー認証処理が必要です。

[APIドメイン]

| ドメイン                                   |
|-------------------------------------------|
| https://api-aifashion.nhncloudservice.com |

[Header]

| 名前 | 値 | 説明 |
| --- | --- | --- |
| Authorization | {secretKey} | コンソールで発行されたセキュリティキー |

### インデックスガイド

* サポートされるファイル形式：JSONL(.jsonl), CSV(.csv)
  * CSVファイルを作成する際は、最初の行から実際のデータを入力する必要があります。
  * ファイルに空白の行がないようにしてください。
  * 内容はUTF-8でエンコードする必要があります。
* インデックスファイルの最大サイズ：5MB
* インデックスファイル内の最大データ数： 10,000個(行区切り)
* 画像URLコンテンツの最大サイズ： 20MB
* サービスごとにインデックス可能な最大文書数： 100,000個


<span id="input-image-guide"></span>
### 画像ガイド

* 画像のダウンロードに3秒以上かかる場合、失敗とみなします。
* 画像内のファッションアイテムの幅と高さが共に20px以下の場合は認識しません。
* 画像サイズが大きくなるにつれて、ファッションアイテムのサイズも大きくすることでより正確に認識できます。
* 画像でファッションアイテムが占める割合が大きいほど、より正確に認識できます。
* 画像URLにポートを直接指定する場合、80、443、10000～12000のポートのみ使用可能です。
* 画像ファイルの最大サイズ：5MB(ただし、[Tag API](#tag-api)は1MB)
* サポートする画像ファイル形式：PNG、JPEG、GIF

<span id="filtering-guide"></span>
### フィルタリングガイド

* フィルタリング機能はカテゴリーまたはフィルタを制限して検索する際に使用します。

#### 対象フィールド
| 名前      | フィールド名     |
|-------------|-------------|
| カテゴリー1depth | category1Id |
| カテゴリー2depth | category2Id |
| カテゴリー3depth | category3Id |
| フィルタ1        | s1          |
| フィルタ2        | s2          |

#### 文法
* `filter.{フィールド名}`=`演算子`:`値`

| 条件         | 演算子 | 例                                                                                                                                   | 説明                                                            |
|----------------|--------|------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| equal(default) | equal  | filter.category2Id=1003,1005 or<br/>filter.category2Id=equal:1003 or<br/>filter.category2Id=equal:1003&filter.s1=equal:1                 | 対象フィールドの値がパラメータの値と同じ文書のみ結果としてレスポンスします。<br/>コンマ(,)で区切ってOR検索が可能です。 |
| not equal      | !equal | filter.category2Id=!equal:1003 or <br/>filter.category2Id=!equal:1003,1005 or<br/>filter.category2Id=!equal:1003,1005&filter.s1=!equal:1 | 対象フィールドの値がパラメータの値と異なる文書のみを結果としてレスポンスします。<br/>コンマ(,)で区切ってOR検索が可能です。 |

<span id="common-response"></span>
### レスポンス共通情報

- 全てのAPIリクエストに'200 OK'でレスポンスします。詳細なレスポンス結果はレスポンス本文ヘッダを参照してください。

[レスポンス本文ヘッダ]

| 名前               | タイプ  | 説明                          |
|----------------------|---------|---------------------------------|
| header.isSuccessful  | boolean | true:正常<br>false:エラー       |
| header.resultCode    | integer     | 0：正常<br>0より大きい：部分成功<br>負の値：エラー |
| header.resultMessage | string  | "SUCCESS":正常<br>その他：エラーメッセージを返す |

[成功レスポンス本文の例]

```json
{
	"header": {
		"isSuccessful": true,
		"resultCode": 0,
		"resultMessage": "SUCCESS"
	}
}
```

[失敗レスポンス本文の例]

```json
{
	"header": {
		"isSuccessful": false,
		"resultCode": -40000,
		"resultMessage": "InvalidParam"
	}
}
```

## サービス管理

### サービスの作成

* サービスを作成できるAPI
* 最大5個まで作成可能
* 許容文字
    * 英語小文字、数字、 (-),(_)
    * 最初の文字は英小文字のみ
    * 2～32文字

#### リクエスト

[URI]

| メソッド | URI                             |
|------|---------------------------------|
| POST | /v2.0/appkeys/{appKey}/services |

[Path Variable]

| 名前 | 説明                  |
|--------|-------------------------|
| appKey | 統合アプリキーまたはサービスアプリキー |

[Request Body]

| 名前      | 説明 |
|-------------|------|
| serviceName | サービス名 |

<details><summary>リクエスト例</summary>

```
curl -X POST "${domain}/v2.0/appkeys/{appKey}/services " -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "serviceName": "my-servicename"
}'
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

<details><summary>レスポンス本文の例</summary>

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

| resultCode | resultMessage                 | 説明                       |
|------------|-------------------------------|--------------------------|
| -40000     | InvalidParam                  | パラメータにエラーがある             |
| -41005     | UnauthorizedAppKeyOrSecretKey | 承認されていないアプリケーションキーまたは秘密鍵 |
| -42010     | DuplicateServiceName          | 重複したサービス名                |
| -42030     | ServiceQuotaExceededException | 許可されたサービス数超過             |
| -50000     | InternalServerError           | サーバーエラー                  |

### サービス削除

* サービスを削除できるAPI
* サービスに処理中のインデックスリクエストがある場合も、サービスを削除できます。

#### リクエスト

[URI]

| メソッド | URI                                           |
|--------|-----------------------------------------------|
| DELETE | /v2.0/appkeys/{appKey}/services/{serviceName} |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名                |

<details><summary>リクエスト例</summary>

```
curl -X DELETE "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

<details><summary>レスポンス本文の例</summary>

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

| resultCode | resultMessage       | 説明         |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | パラメータにエラーがある |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 承認されていないアプリケーションキーまたは秘密鍵 |
| -42000     | NotExistService     | 存在しないサービス |
| -50000     | InternalServerError | サーバーエラー      |

### サービスリスト照会

* 作成したサービスのリストを取得するAPI

#### リクエスト

[URI]

| メソッド | URI                             |
|-----|---------------------------------|
| GET | /v2.0/appkeys/{appKey}/services |

[Path Variable]

| 名前 | 説明                  |
|--------|-------------------------|
| appKey | 統合アプリキーまたはサービスアプリキー |

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/v2.0/appkeys/{appKey}/services"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前                         | タイプ | 必須 | 例     | 説明            |
|--------------------------------|--------|----|------------|-------------------|
| data.totalCount                | integer    | O  | 2          | 総検索結果数    |
| data.items[].serviceName       | string | O  | my-service | サービス名          |
| data.items[].documentCount     | string | O  | 100        | 全文書数      |
| data.items[].remainInsertCount | integer    | O  | 3          | サービスごとに追加可能な文書数 |

<details><summary>レスポンス本文の例</summary>

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
                "serviceName": "my-servicename",
                "documentCount": 100
            },
            {
                "serviceName": "second_service",
                "documentCount": 50
            }
        ]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage       | 説明         |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | パラメータにエラーがある |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 承認されていないアプリケーションキーまたは秘密鍵 |
| -50000     | InternalServerError | サーバーエラー      |


### サービス照会

* サービス名で特定サービスの情報を取得するAPI

#### リクエスト

[URI]

| メソッド | URI                                           |
|-----|-----------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName} |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名                |

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/v2.0/appkeys/{appKey}/services/my-service"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前                 | タイプ  | 必須 | 例     | 説明            |
|------------------------|---------|----|------------|-------------------|
| data.serviceName       | string  | O  | my-service | サービス名          |
| data.documentCount     | string  | O  | 100        | 全文書数      |
| data.remainInsertCount | integer | O  | 3          | サービスごとに追加可能な文書数 |

<details><summary>レスポンス本文の例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "serviceName": "my-service",
        "documentCount": 100,
        "remainInsertCount": 3
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage       | 説明         |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | パラメータにエラーがある |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 承認されていないアプリケーションキーまたは秘密鍵 |
| -50000     | InternalServerError | サーバーエラー      |


## 類似画像商品の推薦

### 商品IDで類似商品を検索

* 商品IDを基に類似商品を検索するAPI

#### リクエスト

[URI]

| メソッド | URI                                                                       |
|-----|---------------------------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/products/{productId}/search |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名                |
| productId   | 商品ID                  |

[URL Parameter]

| 名前             | タイプ  | 必須 | 例   | 説明                                                                                                                                                  |
|--------------------|---------|----|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| limit              | integer | O  | 100      | 最大サイズ<br>1以上200以下に設定可能                                                                                                                         |
| filter.category1Id | string  | X  | equal:3  | category1Id値でフィルタリング                                                                                                                                 |
| filter.category2Id | string  | X  | !equal:3 | category2Id値でフィルタリング                                                                                                                                 |
| filter.category3Id | string  | X  | !equal:3 | category3Id値でフィルタリング                                                                                                                                 |
| filter.s1          | string  | X  | equal:3  | s1値でフィルタリング                                                                                                                                          |
| filter.s2          | string  | X  | !equal:3 | s2値でフィルタリング                                                                                                                                          |
| minSimilarity      | float   | X  | 0.8      | マッチングの有無を判断する類似度の最小基準値<br/> data.items[].similarity >= minSimilarity項目のみマッチングしていると判断します。<br/>0超過1.0以下に設定可能                                     |
| includeDuplicates  | boolean | X  | false    | 重複画像を含めるかどうか(デフォルト値：false)<br/>重複画像を含めるかどうかがfalseの場合、同じ商品は重複除去されるため、レスポンス結果がリクエストした文書数より少なくなる可能性があります。これを望まない場合は、重複画像を含めるかどうかをtrueに設定してリクエストしてください。 |

* filter.category1～3_id, filter.s1～2は[フィルタリングガイド](#filtering-guide)で確認可能

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/products/{productId}/search?limit=100&filter.s1=equal:1"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能
  
[レスポンス本文データ]

| 名前                   | タイプ  | 必須 | 例                       | 説明          |
|--------------------------|---------|----|------------------------------|-----------------|
| data.totalCount          | integer | O  | 100                          | 総検索結果数  |
| data.query               | string  | O  | productId=10234455&limit=100 | 検索クエリ         |
| data.items[].similarity  | float   | O  | 0.91234                      | 検索類似度スコア   |
| data.items[].productId   | string  | O  | 8980335                      | 商品ID          |
| data.items[].name        | string  | O  | AAA red onepiece             | 商品名           |
| data.items[].imageUrl    | string  | O  | http://url.com               | アクセス可能な画像URL  |
| data.items[].category1Id | string  | X  | 72                           | カテゴリー1depth ID |
| data.items[].category2Id | string  | X  | 72                           | カテゴリー2depth ID |
| data.items[].category3Id | string  | X  | 72                           | カテゴリー3depth ID |
| data.items[].s1          | string  | X  | 72                           | 制限検索のためのフィルタ1   |
| data.items[].s2          | string  | X  | 72                           | 制限検索のためのフィルタ2   |


<details><summary>レスポンス本文の例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 1,
        "query": "productID=10234455&limit=100",
        "items": [{
                "similarity": 0.91234,
                "productId": "8980335",
                "name": "AAA red onepiece",
                "imageUrl": "http://url.com"
            }
        ]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage       | 説明         |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | パラメータにエラーがある |
| -40050     | NotFoundProductId   | 商品IDが見つからない  |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 承認されていないアプリケーションキーまたは秘密鍵 |
| -42000     | NotExistService     | 存在しないサービス |
| -50000     | InternalServerError | サーバーエラー      |


## カメラ検索

### ファッションアイテム検出

* 入力された画像からファッションアイテムを検出するAPI

#### リクエスト

[URI]

| メソッド | URI                                                  |
|-----|------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/detect |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名                |

[URL Parameter]

| 名前 | タイプ | 必須 | 例                                    | 説明                 |
|------|--------|----|-------------------------------------------|------------------------|
| path | String | O  | `https://imagecdn.co.kr/sample_image.jpg` | URLエンコードされた画像のURL |

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/detect?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前              | タイプ       | 必須 | 例                                           | 説明                                                  |
|---------------------|--------------|----|--------------------------------------------------|---------------------------------------------------------|
| data.totalCount     | integer      | O  | 100                                              | 総検索結果数                                          |
| data.query          | string       | O  | `path=https://imagecdn.co.kr/sample_image.jpg`   | 検索クエリ                                                 |
| data.items[].link   | string       | O  | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | [検出されたファッションアイテムで類似商品検索](#search-by-detect-link)で使用する値 |
| data.items[].center | double array | O  | [0.825047801147227, 0.330948979591837]           | 検出されたアイテムの中央x, y座標%                                   |
| data.items[].b0     | double array | O  | [0.676864247418738, 0.219377551020408]           | 検出されたアイテムのx0, y0座標%                                    |
| data.items[].b1     | double array | O  | [0.973231355525813, 0.4426204081632654]          | 検出されたアイテムのx1, y1座標%                                    |
| data.items[].score  | float        | O  | 0.9732                                           | 検出されたアイテムの信頼度                                        |

<details><summary>レスポンス本文の例</summary>

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

| resultCode | resultMessage               | 説明                                                |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | パラメータにエラーがある                                        |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 承認されていないアプリケーションキーまたは秘密鍵                                    |
| -42000     | NotExistService             | 存在しないサービス                                       |
| -45020     | ImageTooLargeException      | 画像ファイルのサイズが大きすぎる<br>[画像ガイド](#input-image-guide)参考 |
| -45040     | InvalidImageFormatException | サポートしない画像ファイル形式<br>[画像ガイド](#input-image-guide)参考 |
| -45050     | InvalidImageURLException    | アクセスできないURL                                          |
| -45060     | ImageTimeoutError           | 画像ダウンロードタイムアウト                                    |
| -50000     | InternalServerError         | サーバーエラー                                             |

<span id="search-by-detect-link"></span>
### 検出されたファッションアイテムで類似商品検索

* detect APIからレスポンスで受け取ったlinkを基に類似商品を探すAPIです。

#### リクエスト

[URI]

| メソッド | URI                                                  |
|-----|------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/search |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービスID                 |

[URL Parameter]

| 名前             | タイプ  | 必須 | 例                                             | 説明                                                                                                                                                  |
|--------------------|---------|----|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| limit              | integer | O  | 100                                                | 最大サイズ<br>1以上200以下に設定可能                                                                                                                         |
| link               | string  | O  | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0%3D | detect APIから渡されたlink(URLエンコード必要)                                                                                                                      |
| filter.category1Id | string  | X  | equal:3                                            | category1Id値でフィルタリング                                                                                                                                 |
| filter.category2Id | string  | X  | !equal:3                                           | category2Id値でフィルタリング                                                                                                                                 |
| filter.category3Id | string  | X  | !equal:3                                           | category3Id値でフィルタリング                                                                                                                                 |
| filter.s1          | string  | X  | equal:3                                            | s1値でフィルタリング                                                                                                                                          |
| filter.s2          | string  | X  | !equal:3                                           | s2値でフィルタリング                                                                                                                                          |
| minSimilarity      | float   | X  | 0.8                                                | マッチングの有無を判断する類似度の最小基準値<br/> data.items[].similarity >= minSimilarity項目のみマッチングしていると判断します。<br/>0超過1.0以下に設定可能                                     |
| includeDuplicates  | boolean | X  | false                                              | 重複画像を含めるかどうか(デフォルト値：false)<br/>重複画像を含めるかどうかがfalseの場合、同じ商品は重複除去されるため、レスポンス結果がリクエストした文書数より少なくなる可能性があります。これを望まない場合は、重複画像を含めるかどうかをtrueに設定してリクエストしてください。 |

* filter.category1～3_id, filter.s1～2は[フィルタリングガイド](#filtering-guide)で確認可能

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/image?limit=100&link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0%3D&filter.s1=equal:1"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前                   | タイプ  | 必須 | 例                                                          | 説明          |
|--------------------------|---------|----|-----------------------------------------------------------------|-----------------|
| data.totalCount          | integer | O  | 100                                                             | 総検索結果数  |
| data.query               | string  | O  | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | 検索クエリ         |
| data.items[].similarity  | float   | O  | 0.91234                                                         | 検索類似度スコア   |
| data.items[].productId   | string  | O  | 8980335                                                         | 商品ID          |
| data.items[].name        | string  | O  | AAA red onepiece                                                | 商品名           |
| data.items[].imageUrl    | string  | O  | http://url.com                                                  | アクセス可能な画像URL  |
| data.items[].category1Id | string  | X  | 72                                                              | カテゴリー1depth ID |
| data.items[].category2Id | string  | X  | 72                                                              | カテゴリー2depth ID |
| data.items[].category3Id | string  | X  | 72                                                              | カテゴリー3depth ID |
| data.items[].s1          | string  | X  | 72                                                              | 制限検索のためのフィルタ1   |
| data.items[].s2          | string  | X  | 72                                                              | 制限検索のためのフィルタ2   |


<details><summary>レスポンス本文の例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 1,
        "query": "link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWfnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUiOiJBTEwiLpbnB1dHMi0lt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MyLCJ3aWa0aCI6MTU1LCJozWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0=&limit=100",
        "items": [{
                "similarity": 0.91234,
                "productId": "8980335",
                "name": "AAA red onepiece",
                "imageUrl": "http://url.com"
            }
        ]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage               | 説明                                                |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | パラメータにエラーがある                                        |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 承認されていないアプリケーションキーまたは秘密鍵                                    |
| -42000     | NotExistService             | 存在しないサービス                                       |
| -45020     | ImageTooLargeException      | 画像ファイルのサイズが大きすぎる<br>[画像ガイド](#input-image-guide)参考 |
| -45040     | InvalidImageFormatException | サポートしない画像ファイル形式<br>[画像ガイド](#input-image-guide)参考 |
| -45050     | InvalidImageURLException    | アクセスできないURL                                          |
| -45060     | ImageTimeoutError           | 画像ダウンロードタイムアウト                                    |
| -45070     | NoDetectedFashionItems      | 検出されたファッションアイテムなし                                      |
| -50000     | InternalServerError         | サーバーエラー                                             |

### 画像で類似商品検索

* 画像(URL, File)から認識された最も大きなファッションアイテムを基準に類似商品を探すAPI
* filter.category1～3_id, filter.s1～2は[フィルタリングガイド](#filtering-guide)で確認可能

#### リクエスト

[URI]

| メソッド | URI                                                  |
|------|------------------------------------------------------|
| POST | /v2.0/appkeys/{appKey}/services/{serviceName}/search |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名                |

[Request Body]

**Content-Type : multipart/form-data**

| 名前             | タイプ  | 必須 | 例          | 説明                                                                                                                                                  |
|--------------------|---------|----|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| imageUrl           | string  | △  | http://url.com	 | 画像のURL                                                                                                                                                |
| imageFile          | file    | △  | image.png	      | 画像ファイル                                                                                                                                              |
| limit              | integer | O  | 100             | 最大サイズ<br>1以上200以下に設定可能                                                                                                                         |
| filter.category1Id | string  | X  | equal:3         | category1Id値でフィルタリング                                                                                                                                 |
| filter.category2Id | string  | X  | !equal:3        | category2Id値でフィルタリング                                                                                                                                 |
| filter.category3Id | string  | X  | !equal:3        | category3Id値でフィルタリング                                                                                                                                 |
| filter.s1          | string  | X  | equal:3         | s1値でフィルタリング                                                                                                                                          |
| filter.s2          | string  | X  | !equal:3        | s2値でフィルタリング                                                                                                                                          |
| minSimilarity      | float   | X  | 0.8             | マッチングの有無を判断する類似度の最小基準値<br/> data.items[].similarity >= minSimilarity項目のみマッチングしていると判断します。<br/>0超過1.0以下に設定可能                                     |
| includeDuplicates  | boolean | X  | false           | 重複画像を含めるかどうか(デフォルト値：false)<br/>重複画像を含めるかどうかがfalseの場合、同じ商品は重複除去されるため、レスポンス結果がリクエストした文書数より少なくなる可能性があります。これを望まない場合は、重複画像を含めるかどうかをtrueに設定してリクエストしてください。 |

<details><summary>リクエスト例</summary>

```
curl -X POST -H 'Content-Type: multipart/form-data' -F imageFile=@image.png -F limit=100 "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/search"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前                   | タイプ  | 必須 | 例                       | 説明          |
|--------------------------|---------|----|------------------------------|-----------------|
| data.totalCount          | integer | O  | 100                          | 総検索結果数  |
| data.items[].productId   | string  | O  | 8980335                      | 商品ID          |
| data.items[].similarity  | float   | O  | 0.91234                      | 検索類似度スコア   |
| data.items[].name        | string  | O  | AAA red onepiece             | 商品名           |
| data.items[].imageUrl    | string  | O  | http://url.com               | アクセス可能な画像URL  |
| data.items[].category1Id | string  | X  | 72                           | カテゴリー1depth ID |
| data.items[].category2Id | string  | X  | 72                           | カテゴリー2depth ID |
| data.items[].category3Id | string  | X  | 72                           | カテゴリー3depth ID |
| data.items[].s1          | string  | X  | 72                           | 制限検索のためのフィルタ1   |
| data.items[].s2          | string  | X  | 72                           | 制限検索のためのフィルタ2   |


<details><summary>レスポンス本文の例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 1,
        "query": "productID=10234455&limit=100",
        "items": [{
                "productId": "8980335",
                "similarity": 0.91234,
                "name": "AAA red onepiece",
                "imageUrl": "http://url.com"
            }
        ]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage               | 説明                                                |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | パラメータにエラーがある                                        |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 承認されていないアプリケーションキーまたは秘密鍵                                    |
| -42000     | NotExistService             | 存在しないサービス                                       |
| -45020     | ImageTooLargeException      | 画像ファイルのサイズが大きすぎる<br>[画像ガイド](#input-image-guide)参考 |
| -45040     | InvalidImageFormatException | サポートしない画像ファイル形式<br>[画像ガイド](#input-image-guide)参考 |
| -45050     | InvalidImageURLException    | アクセスできないURL                                          |
| -45060     | ImageTimeoutError           | 画像ダウンロードタイムアウト                                    |
| -45070     | NoDetectedFashionItems      | 検出されたファッションアイテムがない                                      |
| -50000     | InternalServerError         | サーバーエラー                                             |


## ディープタギング

<span id="tag-api"></span>
### ファッションアイテムタグ検出

* 入力画像からファッションアイテムのタグ情報を検出するAPI

#### リクエスト

[URI]

| メソッド | URI                                               |
|-----|---------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/tag |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名                |

[URL Parameter]

| 名前    | タイプ  | 必須 | 例                                    | 説明                                                                                           |
|-----------|---------|----|-------------------------------------------|--------------------------------------------------------------------------------------------------|
| path      | string  | O  | `https://imagecdn.co.kr/sample_image.jpg` | URLエンコードされた画像URL                                                                                 |
| lang      | string  | X  | ko                                        | ラベルの言語(デフォルト値: en)<br/>en: English<br/>ko: Korean<br/>ja: Japanese                                  |
| itemLimit | integer | X  | 3                                         | 画像から見つかったファッションアイテムのうち、タグ情報をレスポンスするアイテムの数<br/>アイテムの幅が長い順にソート(デフォルト値：1)<br/>最大サイズ<br>1以上4以下に設定可能 |

<details><summary>リクエスト例</summary>

```
curl -X GET "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/tag?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=ko&item_limit=3"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前                             | タイプ       | 必須 | 例                                                              | 説明                                       |
|------------------------------------|--------------|----|---------------------------------------------------------------------|----------------------------------------------|
| data.totalCount                    | integer      | O  | 2                                                                   | 総検索結果数                               |
| data.query                         | string       | O  | `path=https://imagecdn.co.kr/sample_image.jpg&lang=ko&item_limit=3` | 検索クエリ                                      |
| data.items[].type                  | string       | O  | JACKET                                                              | 検出されたアイテムのタイプ                            |
| data.items[].score                 | float        | O  | 0.9515                                                              | 検出されたアイテムの信頼度                             |
| data.items[].tags                  | array        | O  |                                                                     | 検出されたアイテムタグ情報の配列                        |
| data.items[].tags[].attribute      | string       | O  | category                                                            | タグのプロパティ                                   |
| data.items[].tags[].labels         | array        | O  |                                                                     | タグラベルの配列                                |
| data.items[].tags[].labels[].label | string       | O  | ブラウス\| Blouse                                                      | タグラベル<br/>URLパラメータのlangによってレスポンス言語が変わる |
| data.items[].tags[].labels[].score | float        | O  | 0.9545                                                              | タグラベルの信頼度                               |
| data.items[].center                | double array | O  | [0.825047801147227, 0.330948979591837]                              | 検出されたアイテムの中央x, y座標%                        |
| data.items[].b0                    | double array | O  | [0.676864247418738, 0.219377551020408]                              | 検出されたアイテムのx0, y0座標%                         |
| data.items[].b1                    | double array | O  | [0.973231355525813, 0.4426204081632654]                             | 検出されたアイテムのx1, y1座標%                         |

<br>
<details><summary>レスポンス本文の例</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 2,
        "query": "path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=ko&item_limit=3",
        "items": [{
            "type": "SHIRT",
            "tags": [{
                "attribute": "category", "labels": [{ "label": "ブラウス", "score": 0.9966272115707397 }]
            }, {
                "attribute": "color", "labels": [{ "label": "ベージュ/アイボリー", "score": 0.7692235112190247 }]
            }, {
                "attribute": "pattern", "labels": [{ "label": "無地", "score": 0.9893960356712341 }]
            }, {
                "attribute": "fabric", "labels": [{ "label": "シルク", "score": 0.586938738822937 }]
            }, {
                "attribute": "neckline", "labels": [{ "label": "シャツの襟", "score": 0.9922573566436768 }]
            }, {
                "attribute": "shoulder", "labels": [{ "label": "バフ/ボリューム", "score": 0.5369117856025696 }]
            }, {
                "attribute": "sleeve_length", "labels": [{ "label": "長袖", "score": 0.6998409032821655 }]
            }, {
                "attribute": "sleeve_shape", "labels": [{ "label": "ストレート", "score": 0.689109206199646 }]
            }, {
                "attribute": "length_up", "labels": [{ "label": "ウエストライン", "score": 0.9575495719909668 }]
            }, {
                "attribute": "age", "labels": [{ "label": "大人", "score": 0.9985153079032898 }]
            }, {
                "attribute": "gender", "labels": [{ "label": "女性", "score": 0.9960111379623413 }]
            }, {
                "attribute": "detail", "labels": [{ "label": "ボタン", "score": 0.9440848231315613 }]
            }, {
                "attribute": "fit", "labels": [{ "label": "基本フィット/レギュラーフィット", "score": 0.789472222328186 }]
            }],
            "center": [ 0.46125, 0.34125 ],
            "b0": [ 0.1875, 0.0175 ],
            "b1": [ 0.735, 0.665 ],
            "score": 0.93118
        }, {
            "type": "SKIRT",
            "tags": [{
                "attribute": "category", "labels": [{ "label": "スカート", "score": 0.9997897744178772 }]
            }, {
                "attribute": "color", "labels": [{ "label": "ブラウン/茶色", "score": 0.8597127199172974 }]
            }, {
                "attribute": "pattern", "labels": [{ "label": "無地", "score": 0.988312304019928 }]
            }, {
                "attribute": "fabric", "labels": [{ "label": "キャンバス", "score": 0.24775846302509308 }]
            }, {
                "attribute": "length_lo", "labels": [{ "label": "ショート", "score": 0.9987099170684814 }]
            }, {
                "attribute": "age", "labels": [{ "label": "大人", "score": 0.9993846416473389 }]
            }, {
                "attribute": "gender", "labels": [{ "label": "女性", "score": 0.9950520396232605 }]
            }, {
                "attribute": "detail", "labels": [{ "label": "ラップスタイル", "score": 0.7058117985725403 }]
            }, {
                "attribute": "fit", "labels": [{ "label": "基本フィット/レギュラーフィット", "score": 0.9844645857810974 }]
            }, {
                "attribute": "shape", "labels": [{ "label": "Aライン/フレア", "score": 0.9432026743888855 }]
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

| resultCode | resultMessage               | 説明                                                |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | パラメータにエラーがある                                        |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 承認されていないアプリケーションキーまたは秘密鍵                                    |
| -42000     | NotExistService             | 存在しないサービス                                       |
| -45020     | ImageTooLargeException      | 画像ファイルのサイズが大きすぎる<br>[画像ガイド](#input-image-guide)参考 |
| -45040     | InvalidImageFormatException | サポートしない画像ファイル形式<br>[画像ガイド](#input-image-guide)参考 |
| -45050     | InvalidImageURLException    | アクセスできないURL                                          |
| -45060     | ImageTimeoutError           | 画像ダウンロードタイムアウト                                    |
| -50000     | InternalServerError         | サーバーエラー                                             |

## インデックス
### インデックスリクエスト

* ファッションアイテムデータをインデックスするAPI
* 渡されたファイルの最初の行を分析して、フォーマットエラーがあるかどうかを検査します。
* 最初の行にエラーが見つからない場合、インデックスのためのキューに入った後、スケジュールに従ってインデックスが作成されます。
* 直接データファイルを送信するか、ダウンロード可能なURLでデータファイルを渡すことができます。
* Content-Type: multipart/form-data

#### ファイルデータフォーマット

| 名前      | field       | value type | 必須 | max length | 備考                             |
|-------------|-------------|------------|----|------------|------------------------------------|
| 商品ID        | productId   | string     | O  | 72         | ユニークキー                       |
| 状態      | status      | string     | O  | 7          | enable:追加またはアップデート<br/>disable:削除 |
| 商品名前    | name        | string     | O  | 256        | 商品名                              |
| カテゴリー1depth | category1Id | string     | O  | 72         | カテゴリー1depth ID                    |
| カテゴリー2depth | category2Id | string     | O  | 72         | カテゴリー2depth ID                    |
| カテゴリー3depth | category3Id | string     | O  | 72         | カテゴリー3depth ID                    |
| 画像url      | imageUrl    | string     | O  | 1000       | アクセス可能な画像URL                     |
| フィルタ1         | s1          | string     | O  | 72         | 制限検索のためのフィルタ1                      |
| フィルタ2         | s2          | string     | O  | 72         | 制限検索のためのフィルタ2                      |

##### JSONLの例
```
{"productId": "10001", "status": "enable", "name": "AAA red onepiece", "category1Id": "1", "category2Id": "1", "category3Id": "2", "imageUrl": "http://aaaaaaa.bbbbb.jpg", "s1": "1", "s2": "2"}
{"productId": "10002", "status": "disable", "name": "BBB blue onepiece", "category1Id": "1", "category2Id": "1", "category3Id": "2", "imageUrl": "http://bbbbbbb.ccccc.jpg", "s1": "s1", "s2": "2"}
{"productId": "10003", "status": "enable", "name": "BBB blue blouse", "category1Id": "1", "category2Id": "1", "category3Id": "3", "imageUrl": "http://bbbbbbb.ddddd.jpg", "s1": "", "s2": "s2"}
...
```

##### CSVの例
```
10001,enable,AAA red onepiece,1,1,2,http://aaaaaaa.bbbbb.jpg,1,2
10002,disable,BBB blue onepiece,1,1,2,http://bbbbbbb.ccccc.jpg,s1,2
10003,enable,BBB blue blouse,1,1,3,http://bbbbbbb.ddddd.jpg,,s2
...
```

#### リクエスト

[URI]

| メソッド | URI                                                   |
|------|-------------------------------------------------------|
| POST | /v2.0/appkeys/{appKey}/services/{serviceName}/indexes |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名                |

[Form Data]

| 名前 | タイプ | 必須かどうか | 例                                               | 説明                                                 |
|--------|--------|-------|------------------------------------------------------|--------------------------------------------------------|
| format | string | O     | jsonl                                                | jsonlまたはcsv                                           |
| link   | string | △     | https://cdn.my-domain.com/202106251000_product.jsonl | データファイルURL                                             |
| file   | file   | △     | @filename                                            | データファイル<br/>linkがfileより優先順位が高く、linkがある場合はfileは無視される |


<details>
<summary>リクエスト例1</summary>

```
curl -X POST "/v2.0/appkeys/{appKey}/services/{serviceName}/indexes" -H "Content-Type: multipart/form-data" -F "file=@/home/user1/202106251000_product.jsonl -F format=jsonl"
```

</details>

<details>
<summary>リクエスト例2</summary>

```
curl -X POST "/v2.0/appkeys/{appKey}/services/{serviceName}/indexes" -H "Content-Type: multipart/form-data" -F "link=https://cdn.my-domain.com/202106251000_product.jsonl -F format=jsonl"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前       | タイプ | 必須 | 例                               | 説明 |
|--------------|--------|----|--------------------------------------|--------|
| data.indexID | string | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | インデックスID |

<details>
<summary>レスポンス本文の例</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "indexID": "24bb94b3-8a6b-488e-b038-4f6038da2596"
    }
}
```

</details>

#### エラーコード
| resultCode | resultMessage               | 説明                               |
|------------|-----------------------------|--------------------------------------|
| -40000     | InvalidParam                | パラメータにエラーがある                      |
| -40010     | InvalidFileError            | ファイル転送にエラーがある場合                 |
| -40020     | NoDataError                 | 転送されたファイルが空のファイルである場合                 |
| -40030     | ExceedDataSizeError         | 転送されたファイルが定められた容量または定められたデータ数を超過した場合 |
| -40080     | TooManyRequestError         | 同時に複数のリクエストを行った場合                |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 承認されていないアプリケーションキーまたは秘密鍵                   |
| -42000     | NotExistService             | 存在しないサービス                      |
| -50000     | InternalServerError         | サーバーエラー                            |


### インデックスリスト照会
* リクエストされたインデックスリストを取得するAPI
* インデックスリクエストに関する情報を知ることができます。
* インデックス情報の最大保管期間は登録時間基準6か月です。

#### リクエスト

[URI]

| メソッド | URI                                                   |
|-----|-------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/indexes |

[Path Variable]

| 名前      | 説明          |
|-------------|-----------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名        |

[URL Parameter]

| 名前   | タイプ       | 必須 | 例             | 説明                                                                                |
|----------|--------------|----|--------------------|---------------------------------------------------------------------------------------|
| start    | integer      | O  | 0                  | 開始インデックス<br/>0から開始                                                                   |
| limit    | integer      | O  | 100                | 最大100<br/>start:0, limit: 100の場合は1から100まで<br/>start: 200, limit: 100の場合は201から300まで |
| order    | string       | X  | requestedTime:desc | ソート条件(デフォルト値: requestedTime:desc)<br/>設定可能条件は[ソート](#indexes-status-order)参照       |
| statuses | string array | X  | running,finished   | インデックスの状態値                                                                          |

#### ページング
* startとlimitパラメータでページングが可能です。
  * start: 0から開始します。
  * limit: 0より大きく、最大100まで可能です。
* 最大ページング可能な数字は1000です。
  * 可能:
    * start: 900
    * end: 100
  * 不可：
    * start: 901
    * end: 100
    * 最大可能なページング数である1000を超えるので不可能です。

<span id="indexes-status-order"></span>
#### ソート
* レスポンス文書のソートパラメータ
* パラメータ形式
  * {ソート可能項目}:{ソート方式}
* ソート可能項目
  * requestedTime:インデックスリクエスト登録時間
  * startTime:インデックス開始時間
  * finishTime:インデックス終了時間
  * addedCount:追加された文書数
  * failedCount:失敗文書数
  * deletedCount:削除文書数
  * exceededCount:制限された使用量を超えた文書数
  * updatedCount:修正文書数
  * totalCount:全文書数
* ソート方式
  * asc:昇順
  * desc:降順

#### インデックス状態
* インデックス状態値を条件として検索できます。
  * reserved:待機
  * running:進行中
  * failed:全体失敗
  * finished:完了(部分失敗を含む)
  * canceled:キャンセル
  * deleted:削除

<details>
<summary>リクエスト例 </summary>

```
curl -X GET "/v2.0/appkeys/{appKey}/services/{serviceName}/indexes?start=0&limit=100&status=running,finished&order=startTime:desc"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前                     | タイプ         | 必須 | 例                               | 説明                                                                                          |
|----------------------------|----------------|----|--------------------------------------|-------------------------------------------------------------------------------------------------|
| data.total                 | integer        | O  | 100                                  | 検索された全文書数                                                                                |
| data.items[].service       | string         | O  | test                                 | サービス名                                                                                        |
| data.items[].id            | string         | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | インデックスID                                                                                           |
| data.items[].filename      | string         | O  | 202106251000_product.jsonl           | インデックスファイル名                                                                                    |
| data.items[].status        | string         | O  | reserved                             | 現在のインデックスの状態を示します。<br/>reserved:待機<br/>running:進行中<br/>failed:全て失敗<br/>finished:完了(部分失敗を含む) |
| data.items[].requestedTime | unix timestamp | O  | 1625098033                           | インデックスリクエスト登録時間                                                                                 |
| data.items[].startTime     | unix timestamp | O  | 1625098033                           | インデックス開始時間                                                                                    |
| data.items[].finishTime    | unix timestamp | O  | 1625098033                           | インデックスが完了した時間                                                                                  |
| data.items[].addedCount    | integer        | O  | 234                                  | 追加された文書数                                                                                   |
| data.items[].failedCount   | integer        | O  | 31                                   | 失敗した文書数<br/>画像ダウンロード失敗などが含まれ、ファッションアイテムが見つからない場合も含む。                                  |
| data.items[].exceededCount | integer        | O  | 31                                   | 制限使用量を超えてインデックスできない文書の数。                                                                  |
| data.items[].deletedCount  | integer        | O  | 31                                   | 削除された文書の数。                                                                                    |
| data.items[].updatedCount  | integer        | O  | 592                                  | 修正された文書の数                                                                                   |
| data.items[].totalCount    | integer        | O  | 888                                  | インデックス総文書数                                                                                  |

<details>
<summary>レスポンス本文の例</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "total": 100,
        "items":[{
            "id": "24bb94b3-8a6b-488e-b038-4f6038da2596",
            "serviceName": "test",
            "filename": "202106251000_product.jsonl",
            "status": "reserved",
            "requestedTime": 1627018935,
            "startTime": 1627018935,
            "finishTime": 1627018935,
            "addedCount": 234,
            "failedCount": 31,
            "exceededCount": 31,
            "deletedCount": 31,
            "updatedCount": 592,
            "totalCount": 888
        }]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage       | 説明         |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | パラメータにエラーがある |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 承認されていないアプリケーションキーまたは秘密鍵 |
| -42000     | NotExistService     | 存在しないサービス |
| -50000     | InternalServerError | サーバーエラー      |


### インデックス詳細照会
* 特定インデックスリクエストに対する詳細情報を知ることができるAPI
* インデックス詳細情報の最大保管期間は登録時間基準6か月です。

#### リクエスト

[URI]

| メソッド | URI                                                             |
|-----|-----------------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/indexes/{indexId} |

[Path Variable]

| 名前      | 説明                  |
|-------------|-------------------------|
| appKey      | 統合アプリキーまたはサービスアプリキー |
| serviceName | サービス名                |
| indexId     | インデックスID                   |

<details>
<summary>リクエスト例 </summary>

```
curl -X GET "/v2.0/appkeys/{appKey}/services/{serviceName}/indexes/{indexId}"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前                          | タイプ         | 必須 | 例                               | 説明                                                                                           |
|---------------------------------|----------------|----|--------------------------------------|--------------------------------------------------------------------------------------------------|
| data.total                      | integer        | O  | 100                                  | 検索された全文書数                                                                                 |
| data.items[].serviceName        | string         | O  | test                                 | サービス名                                                                                         |
| data.items[].id                 | string         | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | インデックスID                                                                                            |
| data.items[].filename           | string         | O  | 202106251000_product.jsonl           | インデックスファイル名                                                                                     |
| data.items[].status             | string         | O  | reserved                             | 現在のインデックス状態を示します。<br/>reserved：待機<br/>running：進行中<br/>failed：全て失敗<br/>finished：完了(部分失敗を含む) |
| data.items[].requestedTime      | unix timestamp | O  | 1625098033                           | インデックスリクエスト登録時間                                                                                  |
| data.items[].startTime          | unix timestamp | O  | 1625098033                           | インデックス開始時間                                                                                     |
| data.items[].finishTime         | unix timestamp | O  | 1625098033                           | インデックスが完了した時間                                                                                   |
| data.items[].addedCount         | integer        | O  | 234                                  | 追加された文書の数                                                                                    |
| data.items[].addedProductIds    | string array   | O  | [10001, 10002]                       | 追加された文書IDリスト                                                                                 |
| data.items[].failedCount        | integer        | O  | 31                                   | 失敗した文書の数<br/>画像ダウンロードの失敗などが含まれ、ファッションアイテムが見つからない場合も含む。                                         |
| data.items[].failedProductIds   | string array   | O  | [10001, 10002]                       | 失敗した文書IDリスト                                                                                 |
| data.items[].exceededCount      | integer        | O  | 31                                   | 制限使用量を超えてインデックスできない文書の数                                                                   |
| data.items[].exceededProductIds | string array   | O  | [10001, 10002]                       | 制限使用量を超えてインデックスできない文書IDのリスト                                                                |
| data.items[].deletedCount       | integer        | O  | 31                                   | 削除された文書の数                                                                                    |
| data.items[].deletedProductIds  | string array   | O  | [10001, 10002]                       | 削除された文書IDリスト                                                                                 |
| data.items[].updatedCount       | integer        | O  | 592                                  | 修正された文書の数                                                                                    |
| data.items[].updatedProductIds  | string array   | O  | [10001, 10002]                       | 修正された文書IDリスト                                                                                 |
| data.items[].totalCount         | integer        | O  | 888                                  | インデックス総文書数                                                                                   |

<details>
<summary>レスポンス本文の例</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "total": 100,
        "items":[{
            "id": "24bb94b3-8a6b-488e-b038-4f6038da2596",
            "serviceName": "test",
            "filename": "202106251000_product.jsonl",
            "status": "reserved",
            "requestedTime": 1627018935,
            "startTime": 1627018935,
            "finishTime": 1627018935,
            "addedCount": 234,
            "addedProductIds": ["10001", "10002"],
            "failedCount": 31,
            "failedProductIds": ["10001", "10002"],
            "exceededCount": 31,
            "exceededProductIds": ["10001", "10002"],
            "deletedCount": 31,
            "deletedProductIds": ["10001", "10002"],
            "updatedCount": 592,
            "updatedProductIds": ["10001", "10002"],
            "totalCount": 888
        }]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage                 | 説明                       |
|------------|-------------------------------|--------------------------|
| -40000     | InvalidParam                  | パラメータにエラーがある             |
| -40090     | NotFoundIndexId               | 索引IDが見つからない              |
| -41005     | UnauthorizedAppKeyOrSecretKey | 承認されていないアプリケーションキーまたは秘密鍵 |
| -42000     | NotExistService               | 存在しないサービス                |
| -50000     | InternalServerError           | サーバーエラー                  |
