## AI Service > AI Fashion > Maker APIガイド

* AI Fashion Makerサービスを使用するために必要なAPIを説明します。

## API共通情報
### 事前準備
* APIを使用するにはプロジェクト統合AppkeyまたはサービスAppkeyが必要です。
    * プロジェクト統合Appkeyを使用することを推奨します。
    * プロジェクト統合Appkeyは、プロジェクト設定ページのAPIセキュリティ設定で作成して使用できます。
    * サービスAppkeyはコンソール上部の**URL & Appkey**メニューで確認が可能です。

### インデックス入力ファイル情報
* utf-8でエンコードされたjsonl、csvファイル形式をサポートします。
    * .csvファイルを作成する時は、最初の行から実際のデータで埋める必要があります。
    * ファイルに空白の行があってはいけません。
* ファイルサイズは最大20MBまで可能で、最大許可文書数は10,000個です。
* 1日最大4回までアップロードが可能で、毎日日本時間0時に初期化されます。
* Service IDごとにインデックス可能な最大文書数は100,000個です。

### リクエスト共通情報
- APIを使用するにはセキュリティキー認証処理が必要です。

[APIドメイン]

| リージョン | ドメイン |
| --- | --- |
| 韓国(パンギョ) | https://kr1-aifashion.api.nhncloudservice.com |
| 韓国(ピョンチョン) | https://kr2-aifashion.api.nhncloudservice.com |
| 韓国(クァンジュ) | https://kr3-aifashion.api.nhncloudservice.com |
| 日本(東京) | https://jp1-aifashion.api.nhncloudservice.com |
| 米国(カリフォルニア) | https://us1-api-aifashion.nhncloudservice.com |

<span id="common-response"></span>
### レスポンス共通情報
- すべてのAPIリクエストに「200 OK」でレスポンスします。詳細なレスポンス結果はレスポンス本文ヘッダを参照します。

[レスポンス本文ヘッダ]

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| header.isSuccessful | boolean | true：正常<br/>false：エラー |
| header.resultCode | int | 0：正常<br/>0より大きい：部分成功<br/>負の数：エラー |
| header.resultMessage | string | "SUCCESS"：正常<br/>その他：エラーメッセージを返す |

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

## API目次

### インデックスリクエスト

* インデックスを作成するデータを伝達します。
* 伝達されたファイルの最初の行を分析してフォーマットエラーがないかをチェックします。
* 最初の行でエラーが検出されない場合、インデックスのためのキューに入った後、スケジュールに基づいてインデックスされます。

#### ファイルデータフォーマット

| 名前 | field | value type | 必須 | max length | 備考 |
| -- | -- | -- | -- | -- | -- |
| 商品ID | product_id | string | O | 72 | unique key |
| 状態 | status | string | O | 7 | enable：追加またはアップデート<br/>disable：削除 |
| 商品名 | name | string | O | 256 | 商品名 |
| カテゴリー1depth | category1_id | string | O | 72 | カテゴリー1depthID |
| カテゴリー2depth | category2_id | string | O | 72 | カテゴリー2depthID |
| カテゴリー3depth | category3_id | string | O | 72 | カテゴリー3depthID |
| 画像url | image_url | string | O | 1000 | アクセス可能な画像URL |
| フィルター1 | s1 | string | O | 72 | 制限検索のためのフィルター1 |
| フィルター2 | s2 | string | O | 72 | 制限検索のためのフィルター2 |

##### 画像ガイド

* 画像のファッションアイテムの幅と高さが両方20px以下の場合は認識しません。
* 画像サイズの大きさに比例してファッションアイテムのサイズも大きくすると正確に認識が可能です。
* 画像内でファッションアイテムが占める割合が大きいほど正確に認識が可能です。
* 画像ファイルの最大サイズ：5,000,000 bytes
* サポート画像フォーマット：PNG、JPEG、GIF
* イメージURLにポートを直接指定する場合は80、443、10000～12000ポートのみ使用できます。

##### jsonl例
```
{"product_id": "10001", "status": "enable", "name": "AAA red onepiece", "category1_id": "1", "category2_id": "1", "category3_id": "2", "image_url": "http://aaaaaaa.bbbbb.jpg", "s1": "1", "s2": "2"}
{"product_id": "10002", "status": "disable", "name": "BBB blue onepiece", "category1_id": "1", "category2_id": "1", "category3_id": "2", "image_url": "http://bbbbbbb.ccccc.jpg", "s1": "s1", "s2": "2"}
{"product_id": "10003", "status": "enable", "name": "BBB blue blouse", "category1_id": "1", "category2_id": "1", "category3_id": "3", "image_url": "http://bbbbbbb.ddddd.jpg", "s1": "", "s2": "s2"}
...
```

##### csv例
```
10001,enable,AAA red onepiece,1,1,2,http://aaaaaaa.bbbbb.jpg,1,2
10002,disable,BBB blue onepiece,1,1,2,http://bbbbbbb.ccccc.jpg,s1,2
10003,enable,BBB blue blouse,1,1,3,http://bbbbbbb.ddddd.jpg,,s2
...
```

#### リクエスト
* 直接データファイルを転送するか、ダウンロード可能なURLでデータファイルを伝達できます。

[URI]

メソッド | URI
--- | ---
POST | /ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| serviceID | 該当Appkeyに所属するservice_id |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| format | string | O | jsonl | jsonlまたはcsv |

[Form Data]

| 名前 | タイプ | 必須かどうか | 例 | 説明 |
| --- | --- | --- | --- | --- |
| link | string | △ | "https://cdn.my-domain.com/202106251000_product.jsonl" | データファイルURL |
| file | file | △ | @filename | データファイル<br/>linkがfileより優先順位が高いため、linkがある場合はfileが無視される |

<details>
<summary>リクエスト例1</summary>

```
curl -X POST "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index?format=jsonl" -H "Content-Type: multipart/form-data" -F "file=@/home/user1/202106251000_product.jsonl"
```

</details>

<details>
<summary>リクエスト例2</summary>

```
curl -X POST "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index?format=jsonl" -F "link=https://cdn.my-domain.com/202106251000_product.jsonl"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.indexID | string | O | 24bb94b3-8a6b-488e-b038-4f6038da2596 | インデックスID |

<details>
<summary>レスポンス本文例</summary>

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
| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000 | InvalidParam | パラメータにエラーがある |
| -40010 | InvalidFileError | ファイル伝達にエラーがある場合 |
| -40020 | NoDataError | 伝達されたファイルが空のファイルの場合 |
| -40030 | ExceedDataSizeError | 伝達されたファイルが規定の容量または規定のデータ数を超過した場合 |
| -40040 | IndexQuotaExceededException | 1日のリクエスト回数を超過した場合 |
| -40080 | TooManyRequestError | 同時に複数回リクエストした場合 |
| -40400 | NoApiFound | 定義されていないapiでリクエストした場合 |
| -41000 | UnauthorizedAppKey | 承認されていないAppkey |
| -42000 | NotExistServiceID | 登録されていないサービスID |
| -50000 | InternalServerError | サーバーエラー |
| 4041007 | URL Not Found | 定義されていないapiでリクエストした場合 |

### サービス情報
* サービスの現在情報を確認します。
    * サービス別の残りインデックス回数
    * サービス別のインデックスされた文書数

#### リクエスト

[URI]

メソッド | URI
--- | ---
GET | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/services
[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |

<details>
<summary>リクエスト例 </summary>

```
curl -X GET "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/services" -H "Content-Type: application/json"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.totalService | int | O | 3 | サービス数 |
| data.items[].documentCnt | int | O | 51128 | 全ての文書数 |
| data.items[].remainInsertCnt | int | O | 3 | 残りデイリーインデックスリクエスト可能回数 |
| data.items[].service | string | O | aaa | 商品で登録したservice_id |

<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalService": 3
        "items": [
            {
                "service": "aaa",
                "remainInsertCnt": 3,
                "documentCnt": 51128
            },
            {
                "service": "bbb",
                "remainInsertCnt": 2,
                "documentCnt": 9778
            },
            {
                "service": "ccc",
                "remainInsertCnt": 0,
                "documentCnt": 29841
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
| -40400 | NoApiFound | 定義されていないapiでリクエストした場合 |
| -41000 | UnauthorizedAppKey | 承認されていないAppkey |
| -50000 | InternalServerError | サーバーエラー |
| 4041007 | URL Not Found | 定義されていないapiでリクエストした場合 |

### インデックス状態照会
* リクエストされたインデックスの現現在状態を確認します。
* インデックス状態の最大保管期間は登録時間基準6か月です。

#### リクエスト

[URI]

メソッド | URI
--- | ---
GET | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/indexes

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| serviceID | 該当Appkeyに所属するservice_id |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| start | int | O | 0 | 開始インデックス<br/>0から開始 |
| limit | int | O | 100 | 最大100<br/>start:0、limit:100の場合は1から100まで<br/>start:200、limit:100の場合は201から300まで |
| order | string | X | "reservedTime:desc" | (デフォルト値)登録時間降順<br/>1つの条件のみ設定可能<br/>設定可能な条件は[ソート](#indexes-status-order)を参照 |
| status | string | X | "finished" | インデックスの状態値 |

#### Paging
* startとlimitパラメータでページングが可能です。
    * start：0から始まります。
    * limit：0より大きくなければならず、最大100まで可能です。
* 最大ページング可能数は1000です。
    * 可能：
        * start：900
        * end：100
    * 不可：
        * start：901
        * end：100
        * 最大ページング可能数である1000を超えるため不可能です。

<span id="indexes-status-order"></span>
#### ソート
* レスポンス文書のソートパラメータ
* パラメータ形式。
    * {ソート可能項目}：{ソート方式}
* ソート可能項目
    * reservedTime：インデックスリクエスト登録時間
    * startTime：インデックス開始時間
    * finishTime：インデックス終了時間
    * addCnt：追加された文書数
    * failCnt：失敗文書数
    * deleteCnt：削除文書数
    * updateCnt：修正文書数
    * totalCnt：文書の総数
* ソート方式
    * asc：昇順
    * desc：降順

#### インデックス状態
* インデックス状態値を条件にして検索できます。
    * reserved：待機
    * running：進行中
    * failed：全て失敗
    * finished：完了（部分失敗を含む）

<details>
<summary>リクエスト例 </summary>

```
curl -X GET "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/indexes?start=0&limit=100&status=running&order=startTime:desc"  -H "Content-Type: application/json"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.total | int | O | 100 | 検索された文書の総数 |
| data.items[].service | String | O | testserviceid | 該当インデックスリクエストが発生したサービス名 |
| data.items[].id | String | O | 24bb94b3-8a6b-488e-b038-4f6038da2596 | インデックスID |
| data.items[].filename | String | O | 202106251000_product.jsonl | インデックスファイル名 |
| data.items[].status | string | O | reserved | 現在のインデックス状態を表します。<br/>reserved：待機<br/>running：進行中<br/>failed：全て失敗<br/>finished：完了(部分失敗を含む) |
| data.items[].reservedTime | unix timestamp | O | 1625098033 | インデックス登録時間 |
| data.items[].startTime | unix timestamp | O | 1625098033 | インデックス開始時間 |
| data.items[].finishTime | unix timestamp | O | 1625098033 | インデックスが完了した時間 |
| data.items[].addCnt | Int | O | 234 | 追加された文書数です。 |
| data.items[].failCnt | Int | O | 31 | 失敗した文書の数<br/>Image Download失敗などが含まれ、ファッションアイテムが見つからなかった場合も含まれる。 |
| data.items[].deleteCnt | Int | O | 31 | 削除された文書数です。 |
| data.items[].updateCnt | int | O | 592 | 修正された文書数です。 |
| data.items[].totalCnt | Int | O | 888 | インデックス文書総数です。 |

<details>
<summary>レスポンス本文例</summary>

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
            "service": "testserviceid"
            "id": "24bb94b3-8a6b-488e-b038-4f6038da2596",
            "filename": "202106251000_product.jsonl",
            "status": "reserved",
            "reservedTime": 1627018935,
            "startTime": 1627018935,
            "finishTime": 1627018935,
            "addCnt": 234,
            "failCnt": 31,
            "deleteCnt": 31,
            "updateCnt": 592,
            "totalCnt": 888
        }]
    }
}
```

</details>

#### エラーコード

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000 | InvalidParam | パラメータにエラーがある |
| -40400 | NoApiFound | 定義されていないapiでリクエストした場合 |
| -41000 | UnauthorizedAppKey | 承認されていないAppkey |
| -42000 | NotExistServiceID | 登録されていないサービスID |
| -50000 | InternalServerError | サーバーエラー |
| 4041007 | URL Not Found | 定義されていないapiでリクエストした場合 |
