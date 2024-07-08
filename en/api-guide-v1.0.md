## AI Service > AI Fashion > API v1.0 Guide

- This guide describes APIs required to use the AI Fashion v1.0 service.

## API Common Information
### Preparations
- To use APIs, an integrated project AppKey or service AppKey is required.
    - We recommend using the integrated project AppKey.
    - You can create and use the integrated project AppKey from the API security settings in the project settings page.
    - The service AppKey can be found in the **URL & Appkey** menu on the top of the console.

### Common Request Information
- The security key needs to be authenticated in order to use APIs.

[API domain]

| Domain                                       |
|-------------------------------------------|
| https://api-aifashion.nhncloudservice.com |

### Indexing guide

* Supported file types: JSONL(.jsonl), CSV(.csv)
  * When you build a CSV file, you need to fill it with actual data from the first line.
  * There should be no blank lines in the file.
  * The content must be UTF-8 encoded.
* Maximum size of the index file: 5 MB
* Maximum number of data in the index file: 10,000 (separated by lines)
* Maximum size of image URL content: 20 MB
* Maximum number of indexable documents per service: 100,000


<span id="input-image-guide"></span>
### Image Guide

* If the image takes more than 3 seconds to download, it is considered a failure.
* If the width and height of a fashion item in an image are both 20px or less, it will not be recognized.
* The larger the image size, the larger the size of the fashion item should be for more accurate recognition.
* The larger the proportion of fashion items in an image, the more accurate the recognition.
* If you specify a port directly in the image URL, only ports 80, 443, and 10000 to 12000 are available.
* Maximum size of the image file: 5 MB (but 1 MB for [](#tag-api)Tag API[](#tag-api))
* Supported image file formats: PNG, JPEG, GIF

<span id="filtering-guide"></span>
### Filtering Guide

* Filtering is used to search by limiting categories or filters.

#### Target Field
| Name          | Field Name          |
|-------------|--------------|
| Category depth 1 | category1_id |
| Category depth 2 | category2_id |
| Category depth 3 | category3_id |
| Filter 1        | s1           |
| Filter 2        | s2           |

#### Grammar
* `filter.{field name}` = `operator` : `value`

| Condition             | Operator    | Examples                                                                                                                                          | Description                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| equal(default) | equal  | filter.category2_id=1003,1005 or<br/>filter.category2_id=equal:1003 or<br/>filter.category2_id=equal:1003&filter.s1=equal:1                 | Only documents where the value of the target field is equal to the value of the parameter are responded as a result.<br/>You can perform an OR search by separating values with commas (,). |
| not equal      | !equal | filter.category2_id=!equal:1003 or <br/>filter.category2_id=!equal:1003,1005 or<br/>filter.category2_id=!equal:1003,1005&filter.s1=!equal:1 | Only documents where the value of the target field is not equal to the value of the parameter are responded as a result.<br/>You can perform an OR search by separating values with commas (,). |

<span id="common-response"></span>
### Common Response Information

- Respond to all API requests with a '200 OK'. For more information on the response results, see Response Body Header.

[Response Body Header]

| Name                   | Type      | Description                              |
|----------------------|---------|---------------------------------|
| header.isSuccessful  | boolean | true: Normal<br>false: Error           |
| header.resultCode    | int     | 0: Normal<br>Greater than 0: Partial success<br>Negative: Error |
| header.resultMessage | string  | "SUCCESS": Normal<br>Other: Returns an error message |

[Success Response Body Example]

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

## Manage services

### Create a service

* APIs to create services
* You can create up to five
* Allowed characters
    * Allow English lowercase letters, numbers, '-', and '_'
    * Allow only lowercase English letters for the first letter
    * Allow 2 to 32 characters

#### Request

[URI]

| Method  | URI                                                       |
|------|-----------------------------------------------------------|
| POST | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| Name        | Description                      |
|-----------|-------------------------|
| appKey    | Integration app key or service app key |
| serviceID | Service Name                    |

<details><summary>Request example</summary>

```
curl -X POST "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### Response

* [Response Body header description omitted].
    * This information is available in [Response Common Information](#common-response).

<details><summary>Response body example</summary>

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

| resultCode | resultMessage                 | Description             |
|------------|-------------------------------|----------------|
| -40000     | InvalidParam                  | The parameter contains an error   |
| -41000     | UnauthorizedAppKey            | Unauthorized AppKeys |
| -42010     | DuplicateServiceName          | Duplicate service names       |
| -42030     | ServiceQuotaExceededException | Exceeding the number of allowed services  |
| -50000     | InternalServerError           | Server error          |

### Delete a service

* APIs to delete services
* You can also delete a service if it has pending index requests.

#### Request

[URI]

| Method    | URI                                                       |
|--------|-----------------------------------------------------------|
| DELETE | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| Name        | Description                      |
|-----------|-------------------------|
| appKey    | Integration app key or service app key |
| serviceID | Service Name                    |

<details><summary>Request example</summary>

```
curl -X DELETE "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### Response

* [Response Body header description omitted].
    * This information is available in [Response Common Information](#common-response).

<details><summary>Response body example</summary>

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

| resultCode | resultMessage       | Description             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | The parameter contains an error   |
| -41000     | UnauthorizedAppKey  | Unauthorized AppKeys |
| -42000     | NotExistService     | Services that don't exist    |
| -50000     | InternalServerError | Server error          |

### Get a list of services

* API to get a list of registered service names

#### Request

[URI]

| Method | URI                                            |
|-----|------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/services |

[Path Variable]

| Name     | Description                      |
|--------|-------------------------|
| appKey | Integration app key or service app key |

<details><summary>Request example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/services"
```

</details>

#### Response

* [Response Body header description omitted].
    * This information is available in [Response Common Information](#common-response).

[Response body data]

| Name                     | Type     | Required | Examples         | Description         |
|------------------------|--------|----|------------|------------|
| data.totalCount        | Number | O  | 5          | Total number of search results |
| data.items[].serviceID | String | O  | my-service | Service Name       |

<details><summary>Response body example</summary>

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

| resultCode | resultMessage       | Description             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | The parameter contains an error   |
| -41000     | UnauthorizedAppKey  | Unauthorized AppKeys |
| -50000     | InternalServerError | Server error          |

## Similar Image Product Recommendations

### Search for similar products by product ID

* API to find products with similar fashion items based on product IDs

#### Request

[URI]

| Method | URI                                                                           |
|-----|-------------------------------------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID} |

[Path Variable]

| Name        | Description                      |
|-----------|-------------------------|
| appKey    | Integration app key or service app key |
| serviceID | Service Name                    |
| productID | Product ID                  |

[URL Parameter]

| Name                  | Type      | Required | Examples       | Description                                                                                                        |
|---------------------|---------|----|----------|-----------------------------------------------------------------------------------------------------------|
| limit               | int     | O  | 100      | Max size<br>Can be set in value from 1 to 200                                                                               |
| filter.category1_id | string  | X  | equal:3  | Filter by category1_id value                                                                                      |
| filter.category2_id | string  | X  | !equal:3 | Filter by category2_id value                                                                                      |
| filter.category3_id | string  | X  | !equal:3 | Filter by category3_id value                                                                                      |
| filter.s1           | string  | X  | equal:3  | Filter by s1 value                                                                                                |
| filter.s2           | string  | X  | !equal:3 | Filter by s2 value                                                                                                |
| threshold           | float32 | X  | 0.8      | Similarity threshold for determining whether an item is matched<br/> Only items with data.items[].similarity >= threshold are determined as matching.<br/>This can be set to a value over 0 and equal to or less than 1.0 |

* filter.category1~3_id, filter.s1~2 can be found in the [](#filtering-guide)Filtering Guide[](#filtering-guide)

<details><summary>Request example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID}?limit=100&filter.s1=equal:1"
```

</details>

#### Response

* [Response Body header description omitted].
    * This information is available in [Response Common Information](#common-response).

[Response body data]

| Name                      | Type     | Required | Examples                           | Description         |
|-------------------------|--------|----|------------------------------|------------|
| data.totalCount         | Number | O  | 100                          | Total number of search results |
| data.query              | String | O  | productID=10234455&limit=100 | Search query      |
| data.items[].similarity | Number | O  | 0.91234                      | Search similarity score  |
| data.items[].productID  | String | O  | 8980335                      | Product ID     |

<details><summary>Response body example</summary>

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

| resultCode | resultMessage       | Description             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | The parameter contains an error   |
| -40050     | NotFoundProductId   | Product ID not found.     |
| -41000     | UnauthorizedAppKey  | Unauthorized AppKeys |
| -42000     | NotExistService     | Services that don't exist    |
| -50000     | InternalServerError | Server error          |

## Search for Cameras

### Detect Fashion Items

* API to detect fashion items from input images

#### Request

[URI]

| Method | URI                                                              |
|-----|------------------------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect |

[Path Variable]

| Name        | Description                      |
|-----------|-------------------------|
| appKey    | Integration app key or service app key |
| serviceID | Service Name                    |

[URL Parameter]

| Name   | Type     | Required | Examples                                        | Description                     |
|------|--------|----|-------------------------------------------|------------------------|
| path | String | O  | `https://imagecdn.co.kr/sample_image.jpg` | URL for URL-encoded images |

<details><summary>Request example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg"
```

</details>

#### Response

* [Response Body header description omitted].
    * This information is available in [Response Common Information](#common-response).

[Response body data]

| Name                  | Type            | Required | Examples                                               | Description                                                      |
|---------------------|---------------|----|--------------------------------------------------|---------------------------------------------------------|
| data.totalCount     | Number        | O  | 100                                              | Total number of search results                                              |
| data.query          | String        | O  | `path=https://imagecdn.co.kr/sample_image.jpg`   | Search query                                                   |
| data.items[].link   | String        | O  | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | Values to use in [similar product search with detected fashion items](#search-by-detect-link) |
| data.items[].center | float64 array | O  | [0.825047801147227, 0.330948979591837]           | Center x, y coordinate % of a detected item                                   |
| data.items[].b0     | float64 array | O  | [0.676864247418738, 0.219377551020408]           | x0, y0 coordinate % of a detected item                                    |
| data.items[].b1     | float64 array | O  | [0.973231355525813, 0.4426204081632654]          | x1, y1 coordinate % of a detected item                                    |
| data.items[].score  | float32       | O  | 0.9732                                           | Confidence score of a detected item                                            |

<details><summary>Response body example</summary>

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

| resultCode | resultMessage               | Description                                                    |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | The parameter contains an error                                          |
| -41000     | UnauthorizedAppKey          | Unauthorized AppKeys                                        |
| -42000     | NotExistService             | Services that don't exist                                           |
| -45020     | ImageTooLargeException      | The size of the image file is too large.<br>See the image guide  |
| -45040     | InvalidImageFormatException | Unsupported image file format.<br>See the image guide |
| -45050     | InvalidImageURLException    | The URL is not accessible.                                          |
| -45060     | ImageTimeoutError           | Image download timeout                                        |
| -50000     | InternalServerError         | Server error                                                 |

<span id="search-by-detect-link"></span>
### Search for similar products with detected fashion items

* API to search for products including similar fashion items based on the link received as the response from detect API.

#### Request

[URI]

| Method | URI                                                             |
|-----|-----------------------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image |

[Path Variable]

| Name        | Description                      |
|-----------|-------------------------|
| appKey    | Integration app key or service app key |
| serviceID | Service Name                    |

[URL Parameter]

| Name                  | Type      | Required | Examples                                                 | Description                                                                                                        |
|---------------------|---------|----|----------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| limit               | int     | O  | 100                                                | Max size<br>Can be set in value from 1 to 200                                                                               |
| link                | string  | O  | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0%3D | link passed from the detect API (URL encoding required)                                                                  |
| filter.category1_id | string  | X  | equal:3                                            | Filter by category1_id value                                                                                      |
| filter.category2_id | string  | X  | !equal:3                                           | Filter by category2_id value                                                                                      |
| filter.category3_id | string  | X  | !equal:3                                           | Filter by category3_id value                                                                                      |
| filter.s1           | string  | X  | equal:3                                            | Filter by s1 value                                                                                                |
| filter.s2           | string  | X  | !equal:3                                           | Filter by s2 value                                                                                                |
| threshold           | float32 | X  | 0.8                                                | Similarity threshold for determining whether an item is matched<br/> Only items with data.items[].similarity >= threshold are determined as matching.<br/>This can be set to a value over 0 and equal to or less than 1.0 |

* filter.category1~3_id, filter.s1~2 can be found in the [](#filtering-guide)Filtering Guide[](#filtering-guide)

<details><summary>Request example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image?limit=100&link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0%3D&filter.s1=equal:1"
```

</details>

#### Response

* [Response Body header description omitted].
    * This information is available in [Response Common Information](#common-response).

[Response body data]

| Name                      | Type     | Required | Examples                                                              | Description         |
|-------------------------|--------|----|-----------------------------------------------------------------|------------|
| data.totalCount         | Number | O  | 100                                                             | Total number of search results |
| data.query              | String | O  | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | Search query      |
| data.items[].similarity | Number | O  | 0.91234                                                         | Search similarity score  |
| data.items[].productID  | String | O  | 8980335                                                         | Product ID     |

<details><summary>Response body example</summary>

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

| resultCode | resultMessage               | Description                                                    |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | The parameter contains an error                                          |
| -41000     | UnauthorizedAppKey          | Unauthorized AppKeys                                        |
| -42000     | NotExistService             | Services that don't exist                                           |
| -45020     | ImageTooLargeException      | The size of the image file is too large.<br>See the image guide  |
| -45040     | InvalidImageFormatException | Unsupported image file format.<br>See the image guide |
| -45050     | InvalidImageURLException    | The URL is not accessible.                                          |
| -45060     | ImageTimeoutError           | Image download timeout                                        |
| -45070     | NoDetectedFashionItems      | No fashion items detected                                         |
| -50000     | InternalServerError         | Server error                                                 |

## Deep Tagging

<span id="tag-api"></span>
### Detect Fashion Tag Items

* API to detect tag information of fashion items in the input image

#### Request

[URI]

| Method | URI                                                           |
|-----|---------------------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag |

[Path Variable]

| Name        | Description              |
|-----------|-----------------|
| appKey    | Integration app key or service app key |
| serviceID | Service Name            |

[URL Parameter]

| Name         | Type     | Required | Examples                                        | Description                                                                                               |
|------------|--------|----|-------------------------------------------|--------------------------------------------------------------------------------------------------|
| path       | String | O  | `https://imagecdn.co.kr/sample_image.jpg` | URL-encoded image URL                                                                                 |
| lang       | String | X  | ko                                        | Language of the label (default: en)<br/>en: English<br/>ko: Korean<br/>jp: Japanese                                  |
| item_limit | int    | X  | 3                                         | Number of items to respond with tag information among fashion items found in the image<br/>Sort items in the descending order of width (default: 1)<br/>Max size<br>Can be set in value from to 1 to 4 |

<details><summary>Request example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=en&item_limit=3"
```

</details>

#### Response

* [Response Body header description omitted].
    * This information is available in [Response Common Information](#common-response).

[Response body data]

| Name                                 | Type                   | Required | Examples                                                                  | Description                                           |
|------------------------------------|----------------------|----|---------------------------------------------------------------------|----------------------------------------------|
| data.totalCount                    | Number               | O  | 2                                                                   | Total number of search results                                   |
| data.query                         | String               | O  | `path=https://imagecdn.co.kr/sample_image.jpg&lang=ko&item_limit=3` | Search query                                        |
| data.items[].type                  | String               | O  | JACKET                                                              | Types of detected items                                |
| data.items[].score                 | float32              | O  | 0.9515                                                              | Confidence score of a detected item                                 |
| data.items[].tags                  | Array of json object | O  |                                                                     | Array of detected item tag information                            |
| data.items[].tags[].attribute      | String               | O  | category                                                            | Tag attribute                                       |
| data.items[].tags[].labels         | Array of json object | O  |                                                                     | Array of tag labels                                    |
| data.items[].tags[].labels[].label | String               | O  | blouse | Blouse                                                      | Tag label<br/>Response language depends on lang in the URL parameter |
| data.items[].tags[].labels[].score | float32              | O  | 0.9545                                                              | Confidence score of tag labels                                   |
| data.items[].center                | float64 array        | O  | [0.825047801147227, 0.330948979591837]                              | Center x, y coordinate % of a detected item                        |
| data.items[].b0                    | float64 array        | O  | [0.676864247418738, 0.219377551020408]                              | x0, y0 coordinate % of a detected item                         |
| data.items[].b1                    | float64 array        | O  | [0.973231355525813, 0.4426204081632654]                             | x1, y1 coordinate % of a detected item                         |

<br>
<details><summary>Response body example</summary>

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
                "attribute": "category", "labels": [{ "label": "Blouse", "score": 0.9966272115707397 }]
            }, {
                "attribute": "color", "labels": [{ "label": "Beige/Ivory", "score": 0.7692235112190247 }]
            }, {
                "attribute": "pattern", "labels": [{ "label": "ignorance", "score": 0.9893960356712341 }]
            }, {
                "attribute": "fabric", "labels": [{ "label": "Silk", "score": 0.586938738822937 }]
            }, {
                "attribute": "neckline", "labels": [{ "label": "shirt collar", "score": 0.9922573566436768 }]
            }, {
                "attribute": "shoulder", "labels": [{ "label": "puff/volume", "score": 0.5369117856025696 }]
            }, {
                "attribute": "sleeve_length", "labels": [{ "label": "Long sleeve", "score": 0.6998409032821655 }]
            }, {
                "attribute": "sleeve_shape", "labels": [{ "label": "Straight/date", "score": 0.689109206199646 }]
            }, {
                "attribute": "length_up", "labels": [{ "label": "Waistline", "score": 0.9575495719909668 }]
            }, {
                "attribute": "age", "labels": [{ "label": "Adult", "score": 0.9985153079032898 }]
            }, {
                "attribute": "gender", "labels": [{ "label": "Female", "score": 0.9960111379623413 }]
            }, {
                "attribute": "detail", "labels": [{ "label": "button", "score": 0.9440848231315613 }]
            }, {
                "attribute": "fit", "labels": [{ "label": "Basic Fit/Regular Fit", "score": 0.789472222328186 }]
            }],
            "center": [ 0.46125, 0.34125 ],
            "b0": [ 0.1875, 0.0175 ],
            "b1": [ 0.735, 0.665 ],
            "score": 0.93118
        }, {
            "type": "SKIRT",
            "tags": [{
                "attribute": "category", "labels": [{ "label": "skirt", "score": 0.9997897744178772 }]
            }, {
                "attribute": "color", "labels": [{ "label": "Brown/Brown", "score": 0.8597127199172974 }]
            }, {
                "attribute": "pattern", "labels": [{ "label": "ignorance", "score": 0.988312304019928 }]
            }, {
                "attribute": "fabric", "labels": [{ "label": "canvas", "score": 0.24775846302509308 }]
            }, {
                "attribute": "length_lo", "labels": [{ "label": "숏", "score": 0.9987099170684814 }]
            }, {
                "attribute": "age", "labels": [{ "label": "Adult", "score": 0.9993846416473389 }]
            }, {
                "attribute": "gender", "labels": [{ "label": "Female", "score": 0.9950520396232605 }]
            }, {
                "attribute": "detail", "labels": [{ "label": "Rapstyle", "score": 0.7058117985725403 }]
            }, {
                "attribute": "fit", "labels": [{ "label": "Basic Fit/Regular Fit", "score": 0.9844645857810974 }]
            }, {
                "attribute": "shape", "labels": [{ "label": "A-line/flare", "score": 0.9432026743888855 }]
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

| resultCode | resultMessage               | Description                                                                           |
|------------|-----------------------------|------------------------------------------------------------------------------|
| -40000     | InvalidParam                | The parameter contains an error                                                                 |
| -41000     | UnauthorizedAppKey          | Unauthorized AppKeys                                                               |
| -42000     | NotExistService             | Services that don't exist                                                                  |
| -45020     | ImageTooLargeException      | The size of the image file is too large.<br>See the image guide  |
| -45040     | InvalidImageFormatException | Unsupported image file format.<br>See the image guide |
| -45050     | InvalidImageURLException    | The URL is not accessible.                                                                 |
| -45060     | ImageTimeoutError           | Image download timeout                                                               |
| -50000     | InternalServerError         | Server error                                                                        |

## Indexing
### Indexing Request

* API to index fashion item data
* Analyze the first line of the provided file and check if there is a format error.
* If no error is found on the first line, the file enters a queue for indexing and is indexed according to the schedule.

#### File Data Format

| Name          | field        | value type | Required | max length | Note                                 |
|-------------|--------------|------------|----|------------|------------------------------------|
| Product ID        | product_id   | string     | O  | 72         | Unique keys                         |
| Status          | status       | string     | O  | 7          | enable: Add or update<br/>disable: Delete |
| Product name        | name         | string     | O  | 256        | Product name                                |
| Category depth 1 | category1_id | string     | O  | 72         | Category depth 1 ID                    |
| Category depth 2 | category2_id | string     | O  | 72         | Category depth 2 ID                    |
| Category depth 3 | category3_id | string     | O  | 72         | Category depth 3 ID                    |
| Image URL      | image_url    | string     | O  | 1000       | Accessible image URL                     |
| Filter1         | s1           | string     | O  | 72         | Filters for limited search1                      |
| Filter2         | s2           | string     | O  | 72         | Filters for limited search2                      |


##### JSONL example
```
{"product_id": "10001", "status": "enable", "name": "AAA red onepiece", "category1_id": "1", "category2_id": "1", "category3_id": "2", "image_url": "http://aaaaaaa.bbbbb.jpg", "s1": "1", "s2": "2"}
{"product_id": "10002", "status": "disable", "name": "BBB blue onepiece", "category1_id": "1", "category2_id": "1", "category3_id": "2", "image_url": "http://bbbbbbb.ccccc.jpg", "s1": "s1", "s2": "2"}
{"product_id": "10003", "status": "enable", "name": "BBB blue blouse", "category1_id": "1", "category2_id": "1", "category3_id": "3", "image_url": "http://bbbbbbb.ddddd.jpg", "s1": "", "s2": "s2"}
...
```

##### CSV example
```
10001,enable,AAA red onepiece,1,1,2,http://aaaaaaa.bbbbb.jpg,1,2
10002,disable,BBB blue onepiece,1,1,2,http://bbbbbbb.ccccc.jpg,s1,2
10003,enable,BBB blue blouse,1,1,3,http://bbbbbbb.ddddd.jpg,,s2
...
```

#### Request
* You can send data files directly or pass data files to a downloadable URL.

[URI]

| Method  | URI                                                                   |
|------|-----------------------------------------------------------------------|
| POST | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index |

[Path Variable]

| Name        | Description                      |
|-----------|-------------------------|
| appKey    | Integration app key or service app key |
| serviceID | Service Name                    |

[URL Parameter]

| Name     | Type     | Required | Examples    | Description           |
|--------|--------|----|-------|--------------|
| format | string | O  | jsonl | jsonl or csv |

[Request Body]

**Content-Type : multipart/form-data**

| Name   | Type     | Required? | Examples                                                     | Description                                                     |
|------|--------|-------|--------------------------------------------------------|--------------------------------------------------------|
| link | string | △     | "https://cdn.my-domain.com/202106251000_product.jsonl" | Data file URL                                             |
| file | file   | △     | @filename                                              | A data file<br/>FILE is ignored if LINK is present because LINK has higher priority than FILE |

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

* [Response Body header description omitted].
  * This information is available in [Response Common Information](#common-response).

[Response body data]

| Name           | Type     | Required | Examples                                   | Description     |
|--------------|--------|----|--------------------------------------|--------|
| data.indexID | string | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | Index ID |

<details>
<summary>Response body example</summary>

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
| resultCode | resultMessage               | Description                                   |
|------------|-----------------------------|--------------------------------------|
| -40000     | InvalidParam                | The parameter contains an error                         |
| -40010     | InvalidFileError            | There is an error in the file transfer.                     |
| -40020     | NoDataError                 | The provided file is empty.                     |
| -40030     | ExceedDataSizeError         | The provided file exceeds the file size limit or data count limit. |
| -40080     | TooManyRequestError         | Multiple requests occurred simultaneously.                    |
| -41000     | UnauthorizedAppKey          | Unauthorized AppKeys                       |
| -42000     | NotExistService             | Services that don't exist                          |
| -50000     | InternalServerError         | Server error                                |

### Get Service Information
* APIs to get information about services
  * Number of indexing left for each service
  * Number of indexed documents for each service

#### Request

[URI]

| Method | URI                                                  |
|-----|------------------------------------------------------|
| GET | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/services |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integration app key or service app key |

<details>
<summary>Request Example</summary>

```
curl -X GET "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/services"
```

</details>

#### Response

* [Response Body header description omitted].
  * This information is available in [Response Common Information](#common-response).

[Response body data]

| Name                           | Type     | Required | Examples    | Description                |
|------------------------------|--------|----|-------|-------------------|
| data.totalService            | int    | O  | 3     | Service count            |
| data.items[].documentCnt     | int    | O  | 51128 | Total document count          |
| data.items[].remainInsertCnt | int    | O  | 3     | Number of index requests per service |
| data.items[].service         | string | O  | aaa   | Service Name              |

<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalService": 3,
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

| resultCode | resultMessage       | Description                  |
|------------|---------------------|---------------------|
| -40000     | InvalidParam        | The parameter contains an error        |
| -41000     | UnauthorizedAppKey  | Unauthorized AppKeys      |
| -50000     | InternalServerError | Server error               |

### Get a list of indexes
* API to get a list of requested indexes
* Information about the index request.
* The maximum retention period for indexed information is 6 months from the time of registration.

#### Request

[URI]

| Method | URI                                                                     |
|-----|-------------------------------------------------------------------------|
| GET | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/indexes |

[Path Variable]

| Name        | Description                      |
|-----------|-------------------------|
| appKey    | Integration app key or service app key |
| serviceID | Service Name                    |

[URL Parameter]

| Name     | Type     | Required | Examples                | Description                                                                                    |
|--------|--------|----|-------------------|---------------------------------------------------------------------------------------|
| start  | int    | O  | 0                 | Start index.<br/>Starts from 0.                                                                     |
| limit  | int    | O  | 100               | Max 100.<br/>start:0, limit: 1 through 100 for 100<br/>start: 200, limit: If 100, then 201 through 300 |
| order  | string | X  | reservedTime:desc | Sort by (default: requestedTime:desc)<br/>For conditions that can set, see [Sorting](#indexes-status-order)           |
| status | string | X  | finished          | Status value of the index                                                                              |

#### Paging
* Paging can be performed with start and limit parameters.
  * start: Starts from 0.
  * limit: Must be greater than 0 and can be up to 100.
* The maximum number for paging is 1000.
  * Yes:
    * start: 900
    * end: 100
  * Impossible:
    * start: 901
    * end: 100
    * This is not possible because it would exceed the maximum possible paging count of 1000.

<span id="indexes-status-order"></span>
#### Sort
* Alignment parameters for response documents
* Parameter formats
  * {sortable items}:{sorting method}
* Sortable items
  * reservedTime: Index request registration time
  * startTime: Index start time
  * finishTime: Index end time
  * addCnt: Number of documents added
  * failCnt: Number of failed documents
  * deleteCnt: Number of documents deleted
  * updateCnt: Number of revised documents
  * totalCnt: Total number of documents
* Sorting Methods
  * asc: Ascending
  * desc: Descending

#### Index status
* You can search for index status values by conditions.
  * reserved: Waiting
  * Running: In Progress
  * failed: All Failure
  * finished: Completed (including partial failures)

<details>
<summary>Request Example</summary>

```
curl -X GET "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/indexes?start=0&limit=100&status=running&order=startTime:desc"
```

</details>

#### Response

* [Response Body header description omitted].
  * This information is available in [Response Common Information](#common-response).

[Response body data]

| Name                        | Type             | Required | Examples                                   | Description                                                                                              |
|---------------------------|----------------|----|--------------------------------------|-------------------------------------------------------------------------------------------------|
| data.total                | int            | O  | 100                                  | Number of total searched documents                                                                                    |
| data.items[].service      | String         | O  | testserviceid                        | The name of the service from which the index request originated                                                                               |
| data.items[].id           | String         | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | Indexing ID                                                                                           |
| data.items[].filename     | String         | O  | 202106251000_product.jsonl           | Indexing file name                                                                                        |
| data.items[].status       | string         | O  | reserved                             | Represents the current indexing status.<br/>reserved: Waiting<br/>Running: In Progress<br/>failed: All Failure<br/>finished: Completed (including partial failures) |
| data.items[].reservedTime | unix timestamp | O  | 1625098033                           | Index request registration time                                                                                     |
| data.items[].startTime    | unix timestamp | O  | 1625098033                           | Index start time                                                                                        |
| data.items[].finishTime   | unix timestamp | O  | 1625098033                           | Indexing finish time                                                                                      |
| data.items[].addCnt       | Int            | O  | 234                                  | Number of added documents                                                                                       |
| data.items[].failCnt      | Int            | O  | 31                                   | Number of failed documents<br/>Including image download failures, and not finding a fashion item.                                  |
| data.items[].deleteCnt    | Int            | O  | 31                                   | Number of deleted documents                                                                                       |
| data.items[].updateCnt    | int            | O  | 592                                  | Number of updated documents                                                                                       |
| data.items[].totalCnt     | Int            | O  | 888                                  | Total number of documents for indexing                                                                                      |

<details>
<summary>Response body example</summary>

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
            "service": "testserviceid",
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
| -40000 | InvalidParam | The parameter contains an error |
| -41000 | UnauthorizedAppKey | Unauthorized AppKeys |
| -42000 | NotExistService | Services that don't exist |
| -50000 | InternalServerError | Server error |
