## AI Service > AI Fashion > Maker API Guide

* This guide describes APIs required to use the AI Fashion Maker service.

## API Common Information
### Prerequisites
* To use the APIs, you need an integrated project Appkey or a service Appkey.
    * We recommend you use an integrated project Appkey.
    * You can use an integrated project Appkey after creating it from the API security settings in the project settings page.
    * You can find a service Appkey in the **URL & Appkey** menu at the top of the Console.

### Indexing Input File Information
* .jsonl and .csv file format encoded in utf-8 is supported.
    * For csv files, you need to fill in the actual data from the first line.
    * There should be no blank lines in the file.
* The maximum file size is 20MB, and the maximum number of documents allowed is 10,000.
* You can upload files up to 4 times per day, and the counting is reset every day at 00:00 Korean time.
* The maximum number of documents that can be indexed per Service ID is 100,000.

### Request Common Information
- To use the APIs, security key authentication is required.

[API Domain]

| Region | Domain |
| --- | --- |
| KOREA (Pangyo) | https://kr1-aifashion.api.nhncloudservice.com |
| KOREA (Pyeongchon) | https://kr2-aifashion.api.nhncloudservice.com |
| KOREA (Gwangju) | https://kr3-aifashion.api.nhncloudservice.com |
| JAPAN (Tokyo) | https://jp1-aifashion.api.nhncloudservice.com |
| US (California) | https://us1-api-aifashion.nhncloudservice.com |

<span id="common-response"></span>
### Response Common Information
- The APIs responds with "200 OK" to all API requests. For more information on the response results, see Response Body Header.

[Response Body Header]

| Name | Type | Description |
| --- | --- | --- |
| header.isSuccessful | boolean | true: Normal<br/>false: Error |
| header.resultCode | int | 0: Normal<br/>Positive number: Partial success<br/>Negative number: Error |
| header.resultMessage | string | "SUCCESS": Normal<br/>Otherwise: Return an error message |

[Example of Success Response Body]

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

[Example of Failure Response Body]

```json
{
    "header": {
        "isSuccessful": false,
        "resultCode": -40000,
        "resultMessage": "InvalidParam"
    }
}
```

## API Contents

### Indexing Request

* Provide the data to index.
* Analyze the first line of the provided file and check if there is a format error.
* If no error is found on the first line, the file enters a queue for indexing and is indexed according to the schedule.

#### File Data Format

| Name | field | value type | Required | max length | Note |
| -- | -- | -- | -- | -- | -- |
| Product ID | product_id | string | O | 72 | unique key |
| Status | status | string | O | 7 | enable: Add or update<br/>disable: Delete |
| Product name | name | string | O | 256 | Product name |
| Category depth 1 | category1_id | string | O | 72 | Category depth 1 ID |
| Category depth 2 | category2_id | string | O | 72 | Category depth 2 ID |
| Category depth 3 | category3_id | string | O | 72 | Category depth 3 ID |
| Image URL | image_url | string | O | 1000 | Accessible image URL |
| Filter 1 | s1 | string | O | 72 | Filter 1 for restricted search |
| FIlter 2 | s2 | string | O | 72 | Filter 2 for restricted search |

##### Image Guide

* If the width and height of a fashion item in the image are both 20px or below, the item is not recognized.
* For more accurate recognition, the size of a fashion item needs to increase as the image size increases.
* For more accurate recognition, a fashion item needs to take up a larger portion in the image.
* Maximum size of image file: 5,000,000 bytes
* Supported image formats: PNG, JPEG, GIF
* If you specify the port directly in the image URL, only ports 80, 443, 10000-12000 can be used.

##### jsonl Example
```
{"product_id": "10001", "status": "enable", "name": "AAA red onepiece", "category1_id": "1", "category2_id": "1", "category3_id": "2", "image_url": "http://aaaaaaa.bbbbb.jpg", "s1": "1", "s2": "2"}
{"product_id": "10002", "status": "disable", "name": "BBB blue onepiece", "category1_id": "1", "category2_id": "1", "category3_id": "2", "image_url": "http://bbbbbbb.ccccc.jpg", "s1": "s1", "s2": "2"}
{"product_id": "10003", "status": "enable", "name": "BBB blue blouse", "category1_id": "1", "category2_id": "1", "category3_id": "3", "image_url": "http://bbbbbbb.ddddd.jpg", "s1": "", "s2": "s2"}
...
```

##### csv Example
```
10001,enable,AAA red onepiece,1,1,2,http://aaaaaaa.bbbbb.jpg,1,2
10002,disable,BBB blue onepiece,1,1,2,http://bbbbbbb.ccccc.jpg,s1,2
10003,enable,BBB blue blouse,1,1,3,http://bbbbbbb.ddddd.jpg,,s2
...
```

#### Request
* You can provide a data file by sending the file directly or providing a downloadable URL.

[URI]

Method | URI
--- | ---
POST | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | service_id that belongs to the Appkey |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| format | string | O | jsonl | jsonl or csv |

[Form Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| link | string | △ | "https://cdn.my-domain.com/202106251000_product.jsonl" | Data file URL |
| file | file | △ | @filename | A data file<br/>link has higher priority than file, so file is ignored if link is available. |

<details>
<summary>Request Example 1</summary>

```
curl -X POST "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index?format=jsonl" -H "Content-Type: multipart/form-data" -F "file=@/home/user1/202106251000_product.jsonl"
```

</details>

<details>
<summary>Request Example 2</summary>

```
curl -X POST "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index?format=jsonl" -F "link=https://cdn.my-domain.com/202106251000_product.jsonl"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.indexID | string | O | 24bb94b3-8a6b-488e-b038-4f6038da2596 | Index ID |

<details>
<summary>Response Body Example</summary>

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

#### Error Codes
| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -40010 | InvalidFileError | There is an error in the file transfer. |
| -40020 | NoDataError | The provided file is empty. |
| -40030 | ExceedDataSizeError | The provided file exceeds the file size limit or data count limit. |
| -40040 | IndexQuotaExceededException | Daily quota for request has been exceeded. |
| -40080 | TooManyRequestError | Multiple requests occurred simultaneously. |
| -40400 | NoApiFound | A request was made with an undefined API. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -50000 | InternalServerError | Server error. |
| 4041007 | URL Not Found | A request was made with an undefined API. |

### Service Information
* Check the current information of services.
    * Number of indexing left for each service
    * Number of indexed documents for each service

#### Request

[URI]

Method | URI
--- | ---
GET | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/services
[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

<details>
<summary>Request Example</summary>

```
curl -X GET "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/services" -H "Content-Type: application/json"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalService | int | O | 3 | Service count |
| data.items[].documentCnt | int | O | 51128 | Total document count |
| data.items[].remainInsertCnt | int | O | 3 | Number of remaining daily indexing request available |
| data.items[].service | string | O | aaa | service_id registered by the product |

<details>
<summary>Response Body Example</summary>

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

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -40400 | NoApiFound | A request was made with an undefined API. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -50000 | InternalServerError | Server error. |
| 4041007 | URL Not Found | A request was made with an undefined API. |

### Indexing Status Query
* Check the current status of requested indexing.
* The maximum retention period for indexing status is 6 months from the registration time.

#### Request

[URI]

Method | URI
--- | ---
GET | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/indexes

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | service_id that belongs to the Appkey |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| start | int | O | 0 | Start index.<br/>Starts from 0. |
| limit | int | O | 100 | Max 100.<br/>start:0, limit:100 indicates 1-100.<br/>start:200, limit:100 indicates 201-300. |
| order | string | X | "reservedTime:desc" | (Default) In descending order of the registration time<br/>Only one condition can be set<br/>For conditions that can set, see ['Sorting'](#indexes-status-order) |
| status | string | X | "finished" | Status value of indexing |

#### Paging
* Paging can be performed with start and limit parameters.
    * start: Starts from 0.
    * limit: Must be higher than 0 and the maximum value is 100.
* The maximum number for paging is 1000.
    * Supported:
        * start: 900
        * end: 100
    * Not supported:
        * start: 901
        * end: 100
        * This is not supported because the number exceeds the maximum number for paging, 1000.

<span id="indexes-status-order"></span>
#### Sorting
* Sorting parameter of the response document
* Parameter format.
    * {Sort item}:{Sorting method}
* Sort item
    * reservedTime: Indexing request registration time
    * startTime: Indexing start time
    * finishTime: Indexing finish time
    * addCnt: Number of added documents
    * failCnt: Number of failed documents
    * deleteCnt: Number of deleted documents
    * updateCnt: Number of updated documents
    * totalCnt: Number of total documents
* Sorting method
    * asc: Ascending order
    * desc: Descending order

#### Indexing Status
* Indexing status value can be searched by conditions.
    * reserved: Pending
    * running: In-progress
    * failed: All failed
    * finished: Finished (including partially failed)

<details>
<summary>Request Example</summary>

```
curl -X GET "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/indexes?start=0&limit=100&status=running&order=startTime:desc"  -H "Content-Type: application/json"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.total | int | O | 100 | Number of total searched documents |
| data.items[].service | String | O | testserviceid | Name of the service for which indexing request occurred |
| data.items[].id | String | O | 24bb94b3-8a6b-488e-b038-4f6038da2596 | Indexing ID |
| data.items[].filename | String | O | 202106251000_product.jsonl | Indexing file name |
| data.items[].status | string | O | reserved | Represents the current indexing status.<br/>reserved: Pending<br/>running: In-progress<br/>failed: All failed<br/>finished: Finished (including partially failed) |
| data.items[].reservedTime | unix timestamp | O | 1625098033 | Indexing registration time |
| data.items[].startTime | unix timestamp | O | 1625098033 | Indexing start time |
| data.items[].finishTime | unix timestamp | O | 1625098033 | Indexing finish time |
| data.items[].addCnt | Int | O | 234 | Number of added documents |
| data.items[].failCnt | Int | O | 31 | Number of failed documents<br/>Includes image download failure and also fashion item detection failure. |
| data.items[].deleteCnt | Int | O | 31 | Number of deleted documents |
| data.items[].updateCnt | int | O | 592 | Number of updated documents |
| data.items[].totalCnt | Int | O | 888 | Total number of documents for indexing |

<details>
<summary>Response Body Example</summary>

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

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -40400 | NoApiFound | A request was made with an undefined API. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -50000 | InternalServerError | Server error. |
| 4041007 | URL Not Found | A request was made with an undefined API. |
