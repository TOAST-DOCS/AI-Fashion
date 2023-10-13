
## AI Service > AI Fashion > Service API Guide

- This guide describes APIs required to use the AI Fashion Search service.

## API Common Information
### Prerequisites
- To use the APIs, you need an integrated project Appkey or a service Appkey.
    - We recommend you use an integrated project Appkey.
    - You can use an integrated project Appkey after creating it from the API security settings in the project settings page.
    - You can find a service Appkey in the **URL & Appkey** menu at the top of the Console.

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

<span id="input-image-guide"></span>
### Input Image Guide

* If it takes longer than 3 seconds to download an input image, it is regarded as a failure.
* If the width and height of a fashion item in the input image are both 20px or below, the item is not recognized.
* For more accurate recognition, the size of a fashion item needs to increase as the image size increases.
* For more accurate recognition, a fashion item needs to take up a larger portion in the image.
* Maximum size of image file: 5,000,000 bytes (For [tag API](#tag-api): 1,000,000 bytes)
* Supported image formats: PNG, JPEG, GIF
* If you specify the port directly in the image URL, only ports 80, 443, 10000-12000 can be used.

<span id="filtering-guide"></span>
### Filtering Guide

* The filtering function is used to search by limiting the categories of the filters.

#### Target Field
| Name | Field Name |
| --- | --- |
| Category depth 1 | category1_id |
| Category depth 2 | category2_id |
| Category depth 3 | category3_id |
| Filter 1 | s1 |
| Filter 2 | s2 |

#### Syntax
* `filter.{field name}` = `operator` : `value`

| Condition | Operator | Example | Description |
| --- | --- | --- | --- |
| equal(default) | equal | filter.category2_id=1003,1005 or<br/>filter.category2_id=equal:1003 | Only documents where the value of the target field is equal to the value of the parameter are responded as a result.<br/>You can perform an OR search by separating values with commas (,). |
| not equal | !equal | filter.category2_id=!equal:1003 or <br/>filter.category2_id=!equal:1003,1005 | Only documents where the value of the target field is not equal to the value of the parameter are responded as a result.<br/>You can perform an OR search by separating values with commas (,). |

<span id="common-response"></span>
### Response Common Information

- The APIs responds with "200 OK" to all API requests. For more information on the response results, see Response Body Header.

[Response Body Header]

| Name | Type | Description |
| --- | --- | --- |
| header.isSuccessful | boolean | true: Normal<br>false: Error |
| header.resultCode | int | 0: Normal<br>Positive number: Partial success<br>Negative number: Error |
| header.resultMessage | string | "SUCCESS": Normal<br>Otherwise: Return an error message |

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

## Service Management

### Register Service ID

* API to register service ID.
* Up to 5 service IDs can be registered.
* Allowed characters
    * Lowercase letters, numbers, '-', '\_' are allowed.
    * 2-32 characters are allowed.
    * Only lowercase letter is allowed for the first character.

#### Request

[URI]

| Method | URI |
| --- | --- |
| POST | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

<details><summary>Request Example</summary>

```
curl -X POST "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

<details><summary>Response Body Example</summary>

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

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42010 | DuplicateServiceID | Duplicated service ID. |
| -42030 | ServiceIDQuotaExceededException | Allowed number of Service IDs has been exceeded. |
| -50000 | InternalServerError | Server error. |

### Delete Service ID

* API to delete registered service ID.
* If an indexing request is in progress for a service ID, the service ID cannot be deleted.

#### Request

[URI]

| Method | URI |
| --- | --- |
| DELETE | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

<details><summary>Request Example</summary>

```
curl -X DELETE "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

<details><summary>Response Body Example</summary>

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

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -42020 | CannotDeletedOnIndexRunning | Cannot be deleted while indexing is running. |
| -50000 | InternalServerError | Server error. |

### Service ID List

* API to check the list of registered service IDs.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/services |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/services"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 5 | Total number of search results |
| data.items[].serviceID | String | O | my-service | Service ID |

<details><summary>Response Body Example</summary>

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

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -50000 | InternalServerError | Server error. |

## Similar Item Recommendations

### Search By ProductID

* API to search for products including similar fashion items based on product ID.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |
| productID | Product ID |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | Max size<br>Can be set in value from 1 to 200 |
| filter.category1_id | string | X | equal:3 | Filter by category1_id value |
| filter.category2_id | string | X | !equal:3 | Filter by category2_id value |
| filter.category3_id | string | X | !equal:3 | Filter by category3_id value |
| filter.s1 | string | X | equal:3 | Filter by s1 value |
| filter.s2 | string | X | !equal:3 | Filter by s2 value |
| threshold | float32 | X | 0.8 | Similarity threshold for determining whether an item is matched<br/> Only items with data.items[].similarity >= threshold are determined as matching.<br/>This can be set to a value over 0 and equal to or less than 1.0 |

* filter.category1~3_id can be found in the [Filtering Guide](./service-api-guide/#filtering-guide)

<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID}?limit=100&filter.s1=equal:1"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | Total number of search results |
| data.query | String | O | productID=10234455&limit=100 | Search query |
| data.items[].similarity | Number | O | 0.91234 | Search similarity score |
| data.items[].productID | String | O | 8980335 | Product ID |

<details><summary>Response Body Example</summary>

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

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -40050 | NotFoundProductIDError | Product ID not found. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -50000 | InternalServerError | Server error. |

## Camera Search

### Detect

* API to detect fashion items in the input image.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| path | String | O | `https://imagecdn.co.kr/sample_image.jpg` | URL of the URL-encoded image |

<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | Total number of search results |
| data.query | String | O | `path=https://imagecdn.co.kr/sample_image.jpg` | Search query |
| data.items[].link | String | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | A link to be used in search by image API. |
| data.items[].center | float64 array | O | [0.825047801147227, 0.330948979591837] | Center x, y coordinate % of a detected item |
| data.items[].b0 | float64 array | O | [0.676864247418738, 0.219377551020408] | x0, y0 coordinate % of a detected item |
| data.items[].b1 | float64 array | O | [0.973231355525813, 0.4426204081632654] | x1, y1 coordinate % of a detected item |
| data.items[].score | float32 | O | 0.9732 | Confidence score of a detected item |

<details><summary>Response Body Example</summary>

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

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -45020 | ImageTooLargeException | The size of the image file is too large.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45040 | InvalidImageFormatException | Unsupported image file format.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45050 | InvalidImageURLException | The URL is not accessible. |
| -45060 | ImageTimeoutError | Image download timeout occurred. |
| -50000 | InternalServerError | Server error. |

### Search By Image

* API to search for products including similar fashion items based on the link received as the response from detect API.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | Max size<br>Can be set in value from 1 to 200 |
| link | string | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0%3D | A link received from detect API (URL encoding is required.) |
| filter.category1_id | string | X | equal:3 | Filter by category1_id value |
| filter.category2_id | string | X | !equal:3 | Filter by category2_id value |
| filter.category3_id | string | X | !equal:3 | Filter by category3_id value |
| filter.s1 | string | X | equal:3 | Filter by s1 value |
| filter.s2 | string | X | !equal:3 | Filter by s2 value |
| threshold | float32 | X | 0.8 | Similarity threshold for determining whether an item is matched<br/> Only items with data.items[].similarity >= threshold are determined as matching.<br/>This can be set to a value over 0 and equal to or less than 1.0 |

* filter.category1~3_id can be found in the [Filtering Guide](./service-api-guide/#filtering-guide)

<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image?limit=100&link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0%3D&filter.s1=equal:1"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | Total number of search results |
| data.query | String | O | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | Search query |
| data.items[].similarity | Number | O | 0.91234 | Search similarity score |
| data.items[].productID | String | O | 8980335 | Product ID |

<details><summary>Response Body Example</summary>

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

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -45020 | ImageTooLargeException | The size of the image file is too large.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45040 | InvalidImageFormatException | Unsupported image file format.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45050 | InvalidImageURLException | The URL is not accessible. |
| -45060 | ImageTimeoutError | Image download timeout occurred. |
| -50000 | InternalServerError | Server error. |

## Deep Tagging

<span id="tag-api"></span>
### Tag

* API to detect tag information of fashion items in the input image.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| path | String | O | `https://imagecdn.co.kr/sample_image.jpg` | URL of the URL-encoded image |
| lang | String | X | ko | Language of the label<br/>default: en<br/>en: English<br/>ko: Korean<br/>jp: Japanese  |
| item_limit | int | X | 3 | Number of items to respond with tag information among fashion items found in the image<br/>Sort items in the descending order of width<br/>default: 1<br/>Maximum size<br>Can be set in value from to 1 to 4 |

<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=en&item_limit=3"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 2 | Total number of search results |
| data.query | String | O | `path=https://imagecdn.co.kr/sample_image.jpg&lang=ko&item_limit=3` | Search query |
| data.items[].type | String | O | JACKET | Type of the detected item |
| data.items[].score | float32 | O | 0.9515 | Confidence score of a detected item |
| data.items[].tags | Array of json object | O |  | Array of detected item tag information |
| data.items[].tags[].attribute | String | O | category | Tag attribute  |
| data.items[].tags[].labels | Array of json object | O |  | Array of tag labels |
| data.items[].tags[].labels[].label | String | O | blouse \| Blouse | Tag label<br/>The response language is different depending on the lang of the URL parameter.  |
| data.items[].tags[].labels[].score | float32 | O | 0.9545 | Confidence score of tag labels |
| data.items[].center | float64 array | O | [0.825047801147227, 0.330948979591837] | Center x, y coordinate % of a detected item |
| data.items[].b0 | float64 array | O | [0.676864247418738, 0.219377551020408] | x0, y0 coordinate % of a detected item |
| data.items[].b1 | float64 array | O | [0.973231355525813, 0.4426204081632654] | x1, y1 coordinate % of a detected item |

<br>
<details><summary>Response Body Example</summary>

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

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -45020 | ImageTooLargeException | The size of the image file is too large.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45040 | InvalidImageFormatException | Unsupported image file format.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45050 | InvalidImageURLException | The URL is not accessible. |
| -45060 | ImageTimeoutError | Image download timeout occurred. |
| -50000 | InternalServerError | Server error. |
