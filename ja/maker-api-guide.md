## AI Service > AI Fashion > Maker APIガイド 

* AI Fashion Makerサービスを使用するために必要なAPIを説明します。

## API共通情報 
### 事前準備 
* APIを使用するにはプロジェクト統合AppkeyまたはサービスAppkeyが必要です。
    * プロジェクト統合Appkeyを使用することを推奨します。
    * プロジェクト統合Appkeyは、プロジェクト設定ページのAPIセキュリティ設定で作成して使用できます。
    * サービスAppkeyはコンソール上部の**URL & Appkey**メニューで確認が可能です。

### インデックス入力ファイル情報
* utf-8でエンコードされたjsonl、csvファイルフォーマットをサポートします。
    * csvファイルの場合、最初の行から実際のデータで埋める必要があります。
* ファイルサイズは最大20MBまで可能で、最大許可文書数は10,000個です。
* 1日最大4回までアップロードが可能で、毎日日本時間0時に初期化されます。

### リクエスト共通情報
- APIを使用するにはセキュリティキー認証処理が必要です。

[APIドメイン]

| 環境 | ドメイン |
| --- | --- |
| Real | https://ai-fashion.cloud.toast.com |

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

## API目次

### Index

* インデックスを作成するデータを伝達します。
* 伝達されたファイルの最初の行を分析してフォーマットエラーがないかをチェックします。
* 最初の行でエラーが検出されない場合、インデックスのためのキューに入った後、スケジュールに基づいてインデックスされます。

#### ファイルデータフォーマット

| 名前 | field | value type | 必須 | max length | 備考 |
| -- | -- | -- | -- | -- | -- |
| 商品id | product_id | string | O | 72 | unique key |
| 状態 |  status | string | O | 7 | enable：追加またはアップデート <br/>disable：削除 |
| 商品名 |  name | string |O |  256 | 商品名 |
| カテゴリー1デプス |  category1_id | string | O | 72 | カテゴリー1デプスID|
| カテゴリー2デプス |  category2_id | string | O | 72 | カテゴリー2デプスID |
| カテゴリー3デプス |  category3_id | string | O | 72 | カテゴリー3デプスID |
| 画像url |  image_url | string |O |  1000 | アクセス可能な画像URL  |

##### 画像ガイド

* 画像のファッションアイテムの幅と高さが両方20px以下の場合は認識しません。
* 画像サイズの大きさに比例してファッションアイテムのサイズも大きくすると正確に認識が可能です。
* 画像内でファッションアイテムが占める割合が大きいほど正確に認識が可能です。
* 画像最大サイズ：最大5,000,000 bytes
* サポート画像フォーマット：PNG、JPEG、GIF

##### jsonl例
```
{"product_id": "10001", "status": "enable", "name": "AAA red onepiece", "category_id1": "A001", "category_id2": "A001001", "category_id3": "A001001001", "image_url": "http://aaaaaaa.bbbbb.jpg"}
{"product_id": "10002", "status": "disable", "name": "BBB blue onepiece", "category_id1": "A001", "category_id2": "A001001", "category_id3": "A001001002", "image_url": "http://bbbbbbb.ccccc.jpg"}
...
```

##### csv例
```
10001,enable,AAA red onepiece,A001,A001001,A001001001,http://aaaaaaa.bbbbb.jpg
10002,disable,BBB blue onepiece,A001,A001001,A001001002,http://bbbbbbb.ccccc.jpg
...
```

#### リクエスト
* 直接データファイルを転送するか、ダウンロード可能なurlを伝達してデータファイルを伝達できます。

[URI]

メソッド | URI
--- | ---
POST | /ai-fashion-maker/v1.0/appkeys/{appKey}/service/{service_id}/index

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| service | 該当Appkeyに所属するservice_id |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| format | string | O | jsonl | jsonlまたはcsv |

[Form Data]

| 名前 | タイプ | 必須かどうか | 例 | 説明 |
| --- | --- | --- | --- | --- |
| link | string |  | "https://cdn.my-domain.com/202106251000_product.jsonl" | データファイルリンク<br>linkがfileより優先順位が高い。<br>linkがある場合は、fileは無視されます |
| file | file |  | @filename | データファイル <br>linkがfileより優先順位が高い。<br>linkがある場合、fileは無視されます |

<details>
<summary>リクエスト例1</summary>

```
curl -X POST "/ai-fashion-maker/v1.0/appkeys/{appKey}/service/{service_id}/index?format=jsonl" -H "Content-Type: multipart/form-file" -F "file=@/home/user1/202106251000_product.jsonl"
```

</details>

<details>
<summary>リクエスト例2</summary>

```
curl -X POST "/ai-fashion-maker/v1.0/appkeys/{appKey}/service/{service_id}/index?format=jsonl -F "link=https://cdn.my-domain.com/202106251000_product.jsonl"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./maker-api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.indexID | string | O | 24bb94b3-8a6b-488e-b038-4f6038da2596 | インデックスID |
| data.docCnt | int | O | 1000 | 文書数 |

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
        "indexID": "24bb94b3-8a6b-488e-b038-4f6038da2596",
        "docCnt": 1000
    }
}
```

</details>

#### Error Codes
| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000| InvalidParam | パラメータにエラーがある |
| -40010| InvalidFileError | ファイル伝達にエラーがある場合 |
| -40020| NoDataError | 伝達されたファイルが空のファイルの場合 |
| -40030| ExceedDataSizeError | 伝達されたファイルが規定の容量または規定のデータ数を超過した場合 |
| -40040| IndexQuotaExceededException | 1日のリクエスト回数を超過した場合 |
| -40080| TooManyRequestError | 同時に複数回リクエストした場合 |
| -41000| UnauthorizedAppKey | 承認されていないAppkey |
| -50000| InternalServerError | サーバーエラー |

### service info
* サービスの現在情報を確認します。 
    * サービス別の残りインデックス回数
    * サービス別のインデックスされた文書数

#### リクエスト

[URI]

メソッド | URI
--- | ---
GET | /ai-fashion-maker/v1.0/appkeys/{appKey}/services
[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |

<details>
<summary>リクエスト例 </summary>

```
curl -X GET "/ai-fashion-maker/v1.0/appkeys/{appKey}/service/info" -H "Content-Type: application/json"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./maker-api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.totalService | int | O | 3 | サービス数 |
| data.items[].documentCnt | int | O | 51128 | 全ての文書数 |
| data.items[].remainInsertCnt | int | O | 3 | Index可能回数 |
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

#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000| InvalidParam | パラメータにエラーがある |
| -41000| UnauthorizedAppKey | 承認されていないAppkey |
| -50000| InternalServerError | サーバーエラー |

### Indexes Status
* Indexの現在状態を確認します。 
* index status最大保管期間は登録時間基準6か月です。

#### リクエスト

[URI]

メソッド | URI
--- | ---
GET | /ai-fashion-maker/v1.0/appkeys/{appKey}/service/{service_id}/indexes

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| service | 該当Appkeyに所属するservice_id |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| start | int | O | 0 | 開始index。<br>0から開始。 |
| limit | int | O | 100 | 最大100。<br>start：0、limit：100の場合1から100まで。 <br>start：200、limit：100の場合、201から300まで。 |
| order | string | X | "reservedTime:desc" | 基本値。登録時間降順。ソート条件は最大1個可能 |
| status | string | X | "finished" | インデックスの状態値 |

#### paging
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

#### ソート
* レスポンス文書のソートパラメータ
* パラメータ形式。 
    * `ソート可能項目`：`ソート方式`
* ソート可能項目
    * reservedTime：index登録時間
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
curl -X GET "/ai-fashion-maker/v1.0/appkeys/{appKey}/indexes?start=0&limit=100&status=running&order=startTime:desc"  -H "Content-Type: application/json"
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./maker-api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.total | int | O | 100 | 検索された文書の総数 |
| data.items[].service | String | O | testserviceid | 該当インデックスリクエストが発生したサービス名 |
| data.items[].id | String | O | 24bb94b3-8a6b-488e-b038-4f6038da2596 | インデックスid |
| data.items[].filename | String | O | 202106251000_product.jsonl | インデックスファイル名を表します。 |
| data.items[].status | string | O | reserved | 現在のインデックス状態を表します。<br>reserved：待機<br>running：進行中 <br>failed：全て失敗<br/>finished：完了(部分失敗を含む) |
| data.items[].reservedTime | unix timestamp | O | 1625098033 | インデックス登録時間 |
| data.items[].startTime | unix timestamp | O | 1625098033 | インデックス開始時間 |
| data.items[].finishTime | unix timestamp | O | 1625098033 | インデックスが完了した時間 |
| data.items[].addCnt | Int | O | 234 | 追加された文書数です。 |
| data.items[].failCnt | Int | O | 31 | 失敗した文書数です。<br/>Image Download失敗などが含まれ、Fashion Item Detect失敗も含まれます。 |
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

#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| -40000| InvalidParam | パラメータにエラーがある |
| -41000| UnauthorizedAppKey | 承認されていないAppkey |
| -50000| InternalServerError | サーバーエラー |
