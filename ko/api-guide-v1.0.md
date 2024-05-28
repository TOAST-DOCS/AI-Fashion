## AI Service > AI Fashion > API v1.0 가이드

- AI Fashion v1.0 서비스를 사용하는 데 필요한 API를 설명합니다.

## API 공통 정보
### 사전 준비
- API 사용을 위해서는 프로젝트 통합 Appkey 또는 서비스 Appkey가 필요합니다.
    - 프로젝트 통합 Appkey 사용을 권장합니다.
    - 프로젝트 통합 Appkey는 프로젝트 설정 페이지의 API 보안 설정에서 생성해 사용할 수 있습니다.
    - 서비스 Appkey는 콘솔 상단 **URL & Appkey** 메뉴에서 확인이 가능합니다.

### 요청 공통 정보
- API를 사용하기 위해서는 보안 키 인증 처리가 필요합니다.

[API 도메인]

| 도메인                                       |
|-------------------------------------------|
| https://api-aifashion.nhncloudservice.com |

### 색인 가이드

* 지원되는 파일 형식: .jsonl, .csv
  * .csv 파일을 작성할 때는 첫 줄부터 실제 데이터로 채워야 합니다.
  * 파일에 빈 줄이 없어야 합니다.
  * 내용은 utf-8로 인코딩되어야 합니다.
* 색인 파일의 최대 크기: 5MB
* 색인 파일 내 데이터 최대 개수: 10,000개(줄로 구분)
* 이미지 url 컨텐츠의 최대 크기: 20MB
* 서비스당 색인 가능한 최대 문서 수: 100,000개


<span id="input-image-guide"></span>
### 이미지 가이드

* 이미지 다운로드에 3초 이상 걸리면 실패로 간주합니다.
* 이미지에서 패션 아이템의 너비와 높이가 모두 20px 이하인 경우에는 인식하지 않습니다.
* 이미지 크기가 커질수록 패션 아이템의 크기도 커져야 더 정확하게 인식할 수 있습니다.
* 이미지에서 패션 아이템이 차지하는 비중이 클수록 더 정확하게 인식할 수 있습니다.
* 이미지 URL에 포트를 직접 지정하는 경우 80, 443, 10000~12000 포트만 사용 가능합니다.
* 이미지 파일의 최대 크기: 5MB(단, [Tag API](#tag-api)는 1MB)
* 지원 이미지 파일 형식: PNG, JPEG, GIF

<span id="filtering-guide"></span>
### 필터링 가이드

* 필터링 기능은 카테고리 또는 필터를 제한하여 검색할 때 사용합니다.

#### 대상 필드
| 이름          | 필드명          |
|-------------|--------------|
| 카테고리 1depth | category1_id |
| 카테고리 2depth | category2_id |
| 카테고리 3depth | category3_id |
| 필터 1        | s1           |
| 필터 2        | s2           |

#### 문법
* `filter.{필드명}`=`연산자`:`값`

| 조건             | 연산자    | 예제                                                                                                                                          | 설명                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| equal(default) | equal  | filter.category2_id=1003,1005 or<br/>filter.category2_id=equal:1003 or<br/>filter.category2_id=equal:1003&filter.s1=equal:1                 | 대상 필드의 값이 파라미터 값과 같은 문서만 결과로 응답합니다.<br/>콤마(,)로 구분하여 OR 검색이 가능합니다. |
| not equal      | !equal | filter.category2_id=!equal:1003 or <br/>filter.category2_id=!equal:1003,1005 or<br/>filter.category2_id=!equal:1003,1005&filter.s1=!equal:1 | 대상 필드의 값이 파라미터 값과 다른 문서만 결과로 응답합니다.<br/>콤마(,)로 구분하여 OR 검색이 가능합니다. |

<span id="common-response"></span>
### 응답 공통 정보

- 모든 API 요청에 '200 OK'로 응답합니다. 자세한 응답 결과는 응답 본문 헤더를 참고합니다.

[응답 본문 헤더]

| 이름                   | 타입      | 설명                              |
|----------------------|---------|---------------------------------|
| header.isSuccessful  | boolean | true: 정상<br>false: 오류           |
| header.resultCode    | int     | 0: 정상<br>0보다 큼: 부분 성공<br>음수: 오류 |
| header.resultMessage | string  | "SUCCESS": 정상<br>그 외: 오류 메시지 반환 |

[성공 응답 본문 예]

```json
{
	"header": {
		"isSuccessful": true,
		"resultCode": 0,
		"resultMessage": "SUCCESS"
	}
}
```

[실패 응답 본문 예]

```json
{
	"header": {
		"isSuccessful": false,
		"resultCode": -40000,
		"resultMessage": "InvalidParam"
	}
}
```

## 서비스 관리

### 서비스 등록

* 서비스를 등록할 수 있는 API
* 최대 5개까지 등록 가능
* 허용 문자
    * 영어 소문자, 숫자, '-', '\_' 허용
    * 첫 글자는 영어 소문자만 허용
    * 2~32 글자 허용

#### 요청

[URI]

| 메서드  | URI                                                       |
|------|-----------------------------------------------------------|
| POST | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| 이름        | 설명                      |
|-----------|-------------------------|
| appKey    | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스명                    |

<details><summary>요청 예</summary>

```
curl -X POST "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

<details><summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage                 | 설명             |
|------------|-------------------------------|----------------|
| -40000     | InvalidParam                  | 파라미터에 오류가 있음   |
| -41000     | UnauthorizedAppKey            | 승인되지 않은 Appkey |
| -42010     | DuplicateServiceName          | 중복된 서비스명       |
| -42030     | ServiceQuotaExceededException | 허용된 서비스 개수 초과  |
| -50000     | InternalServerError           | 서버 오류          |

### 서비스 삭제

* 등록된 서비스를 삭제할 수 있는 API
* 서비스에 처리중인 색인 요청이 있는 경우도, 서비스를 삭제할 수 있습니다.

#### 요청

[URI]

| 메서드    | URI                                                       |
|--------|-----------------------------------------------------------|
| DELETE | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| 이름        | 설명                      |
|-----------|-------------------------|
| appKey    | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스명                    |

<details><summary>요청 예</summary>

```
curl -X DELETE "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

<details><summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage       | 설명             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음   |
| -41000     | UnauthorizedAppKey  | 승인되지 않은 Appkey |
| -42000     | NotExistService     | 등록되지 않은 서비스    |
| -50000     | InternalServerError | 서버 오류          |

### 서비스 목록

* 등록된 서비스명 목록을 확인할 수 있는 API

#### 요청

[URI]

| 메서드 | URI                                            |
|-----|------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/services |

[Path Variable]

| 이름     | 설명                      |
|--------|-------------------------|
| appKey | 통합 Appkey 또는 서비스 Appkey |

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/services"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                     | 타입     | 필수 | 예제         | 설명         |
|------------------------|--------|----|------------|------------|
| data.totalCount        | Number | O  | 5          | 총 검색 결과 개수 |
| data.items[].serviceID | String | O  | my-service | 서비스명       |

<details><summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage       | 설명             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음   |
| -41000     | UnauthorizedAppKey  | 승인되지 않은 Appkey |
| -50000     | InternalServerError | 서버 오류          |

## 유사 이미지 상품 추천

### Search By ProductID

* 상품 아이디를 기반으로 유사한 패션 아이템을 포함한 상품을 찾아주는 API

#### 요청

[URI]

| 메서드 | URI                                                                           |
|-----|-------------------------------------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID} |

[Path Variable]

| 이름        | 설명                      |
|-----------|-------------------------|
| appKey    | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스명                    |
| productID | 상품 아이디                  |

[URL Parameter]

| 이름                  | 타입      | 필수 | 예제       | 설명                                                                                                        |
|---------------------|---------|----|----------|-----------------------------------------------------------------------------------------------------------|
| limit               | int     | O  | 100      | 최대 크기<br>1 이상 200 이하로 설정 가능                                                                               |
| filter.category1_id | string  | X  | equal:3  | category1_id 값으로 필터링                                                                                      |
| filter.category2_id | string  | X  | !equal:3 | category2_id 값으로 필터링                                                                                      |
| filter.category3_id | string  | X  | !equal:3 | category3_id 값으로 필터링                                                                                      |
| filter.s1           | string  | X  | equal:3  | s1 값으로 필터링                                                                                                |
| filter.s2           | string  | X  | !equal:3 | s2 값으로 필터링                                                                                                |
| threshold           | float32 | X  | 0.8      | 매칭 여부를 판단하는 유사도 기준값<br/> data.items[].similarity >= threshold인 항목만 매칭되는 것으로 판단합니다.<br/>0 초과 1.0 이하로 설정 가능 |
| includeDuplicates  | boolean | X  | false    | 중복 이미지 포함 여부 (기본적으로 동일한 상품에 대한 중복 제거가 이루어 지므로, 응답 결과가 요청한 문서 수 보다 적을 수 있습니다. 이를 원치 않으시면 중복 제거 옵션을 false 로 설정해서 요청하시기를 바랍니다.)             |

* filter.category1~3_id, filter.s1~2는 [필터링 가이드](#filtering-guide)에서 확인 가능

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID}?limit=100&filter.s1=equal:1"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                      | 타입     | 필수 | 예제                           | 설명         |
|-------------------------|--------|----|------------------------------|------------|
| data.totalCount         | Number | O  | 100                          | 총 검색 결과 개수 |
| data.query              | String | O  | productID=10234455&limit=100 | 검색 질의      |
| data.items[].similarity | Number | O  | 0.91234                      | 검색 유사도 점수  |
| data.items[].productID  | String | O  | 8980335                      | 상품 아이디     |

<details><summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage       | 설명             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음   |
| -40050     | NotFoundProductId   | 상품 아이디가 없음     |
| -41000     | UnauthorizedAppKey  | 승인되지 않은 Appkey |
| -42000     | NotExistService     | 등록되지 않은 서비스    |
| -50000     | InternalServerError | 서버 오류          |

## 카메라 검색

### Detect

* 입력 이미지에서 패션 아이템을 감지하는 API입니다.

#### 요청

[URI]

| 메서드 | URI                                                              |
|-----|------------------------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect |

[Path Variable]

| 이름        | 설명                      |
|-----------|-------------------------|
| appKey    | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스명                    |

[URL Parameter]

| 이름   | 타입     | 필수 | 예제                                        | 설명                     |
|------|--------|----|-------------------------------------------|------------------------|
| path | String | O  | `https://imagecdn.co.kr/sample_image.jpg` | URL Encoding된 이미지의 URL |

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                  | 타입            | 필수 | 예제                                               | 설명                       |
|---------------------|---------------|----|--------------------------------------------------|--------------------------|
| data.totalCount     | Number        | O  | 100                                              | 총 검색 결과 개수               |
| data.query          | String        | O  | `path=https://imagecdn.co.kr/sample_image.jpg`   | 검색 질의                    |
| data.items[].link   | String        | O  | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | search by image에서 사용할 링크 |
| data.items[].center | float64 array | O  | [0.825047801147227, 0.330948979591837]           | 감지된 아이템의 중앙 x, y 좌표 %    |
| data.items[].b0     | float64 array | O  | [0.676864247418738, 0.219377551020408]           | 감지된 아이템의 x0, y0 좌표 %     |
| data.items[].b1     | float64 array | O  | [0.973231355525813, 0.4426204081632654]          | 감지된 아이템의 x1, y1 좌표 %     |
| data.items[].score  | float32       | O  | 0.9732                                           | 감지된 아이템의 신뢰도             |

<details><summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage               | 설명                                                                           |
|------------|-----------------------------|------------------------------------------------------------------------------|
| -40000     | InvalidParam                | 파라미터에 오류가 있음                                                                 |
| -41000     | UnauthorizedAppKey          | 승인되지 않은 Appkey                                                               |
| -42000     | NotExistService             | 등록되지 않은 서비스                                                                  |
| -45020     | ImageTooLargeException      | 이미지 파일의 크기가 너무 큼<br>[이미지 가이드](#input-image-guide) 참고  |
| -45040     | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[이미지 가이드](#input-image-guide) 참고 |
| -45050     | InvalidImageURLException    | 접근할 수 없는 URL                                                                 |
| -45060     | ImageTimeoutError           | 이미지 다운로드 시간 초과                                                               |
| -45070     | NoDetectedFashionItems      | 감지된 패션 아이템 없음                                        |
| -50000     | InternalServerError         | 서버 오류                                                                        |

### 패션 아이템 감지 link로 검색

* detect api에서 응답으로 받은 link를 기반으로 유사한 패션 아이템을 포함한 상품을 찾아주는 API

#### 요청

[URI]

| 메서드 | URI                                                             |
|-----|-----------------------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image |

[Path Variable]

| 이름        | 설명                      |
|-----------|-------------------------|
| appKey    | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스명                    |

[URL Parameter]

| 이름                  | 타입      | 필수 | 예제                                                 | 설명                                                                                                        |
|---------------------|---------|----|----------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| limit               | int     | O  | 100                                                | 최대 크기<br>1 이상 200 이하로 설정 가능                                                                               |
| link                | string  | O  | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0%3D | detect API에서 전달받은 link (URL encoding 필요)                                                                  |
| filter.category1_id | string  | X  | equal:3                                            | category1_id 값으로 필터링                                                                                      |
| filter.category2_id | string  | X  | !equal:3                                           | category2_id 값으로 필터링                                                                                      |
| filter.category3_id | string  | X  | !equal:3                                           | category3_id 값으로 필터링                                                                                      |
| filter.s1           | string  | X  | equal:3                                            | s1 값으로 필터링                                                                                                |
| filter.s2           | string  | X  | !equal:3                                           | s2 값으로 필터링                                                                                                |
| threshold           | float32 | X  | 0.8                                                | 매칭 여부를 판단하는 유사도 기준값<br/> data.items[].similarity >= threshold인 항목만 매칭되는 것으로 판단합니다.<br/>0 초과 1.0 이하로 설정 가능 |
| includeDuplicates  | boolean | X  | false    | 중복 이미지 포함 여부 (기본적으로 동일한 상품에 대한 중복 제거가 이루어 지므로, 응답 결과가 요청한 문서 수 보다 적을 수 있습니다. 이를 원치 않으시면 중복 제거 옵션을 false 로 설정해서 요청하시기를 바랍니다.)             |

* filter.category1~3_id, filter.s1~2는 [필터링 가이드](#filtering-guide)에서 확인 가능

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image?limit=100&link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0%3D&filter.s1=equal:1"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                      | 타입     | 필수 | 예제                                                              | 설명         |
|-------------------------|--------|----|-----------------------------------------------------------------|------------|
| data.totalCount         | Number | O  | 100                                                             | 총 검색 결과 개수 |
| data.query              | String | O  | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | 검색 질의      |
| data.items[].similarity | Number | O  | 0.91234                                                         | 검색 유사도 점수  |
| data.items[].productID  | String | O  | 8980335                                                         | 상품 아이디     |

<details><summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage               | 설명                                                                           |
|------------|-----------------------------|------------------------------------------------------------------------------|
| -40000     | InvalidParam                | 파라미터에 오류가 있음                                                                 |
| -41000     | UnauthorizedAppKey          | 승인되지 않은 Appkey                                                               |
| -42000     | NotExistService             | 등록되지 않은 서비스                                                                  |
| -45020     | ImageTooLargeException      | 이미지 파일의 크기가 너무 큼<br>[이미지 가이드](#input-image-guide) 참고  |
| -45040     | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[이미지 가이드](#input-image-guide) 참고 |
| -45050     | InvalidImageURLException    | 접근할 수 없는 URL                                                                 |
| -45060     | ImageTimeoutError           | 이미지 다운로드 시간 초과                                                               |
| -50000     | InternalServerError         | 서버 오류                                                                        |

## 딥 태깅

<span id="tag-api"></span>
### Tag

* 입력 이미지에서 패션 아이템의 태그 정보를 감지하는 API입니다.

#### 요청

[URI]

| 메서드 | URI                                                           |
|-----|---------------------------------------------------------------|
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag |

[Path Variable]

| 이름        | 설명                      |
|-----------|-------------------------|
| appKey    | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스명                    |

[URL Parameter]

| 이름         | 타입     | 필수 | 예제                                        | 설명                                                                                                      |
|------------|--------|----|-------------------------------------------|---------------------------------------------------------------------------------------------------------|
| path       | String | O  | `https://imagecdn.co.kr/sample_image.jpg` | URL Encode된 이미지 URL                                                                                     |
| lang       | String | X  | ko                                        | label의 언어<br/>default: en<br/>en: English<br/>ko: Korean<br/>jp: Japanese                               |
| item_limit | int    | X  | 3                                         | 이미지에서 발견된 패션 아이템 중 태그 정보를 응답할 아이템 숫자<br/>아이템의 너비가 긴 순서로 정렬<br/>default: 1<br/>최대 크기<br>1 이상 4 이하로 설정 가능 |

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=ko&item_limit=3"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                                 | 타입                   | 필수 | 예제                                                                  | 설명                                           |
|------------------------------------|----------------------|----|---------------------------------------------------------------------|----------------------------------------------|
| data.totalCount                    | Number               | O  | 2                                                                   | 총 검색 결과 개수                                   |
| data.query                         | String               | O  | `path=https://imagecdn.co.kr/sample_image.jpg&lang=ko&item_limit=3` | 검색 질의                                        |
| data.items[].type                  | String               | O  | JACKET                                                              | 감지된 아이템의 type                                |
| data.items[].score                 | float32              | O  | 0.9515                                                              | 감지된 아이템의 신뢰도                                 |
| data.items[].tags                  | Array of json object | O  |                                                                     | 감지된 아이템 태그 정보의 배열                            |
| data.items[].tags[].attribute      | String               | O  | category                                                            | 태그의 속성                                       |
| data.items[].tags[].labels         | Array of json object | O  |                                                                     | 태그 라벨의 배열                                    |
| data.items[].tags[].labels[].label | String               | O  | 블라우스 \| Blouse                                                      | 태그 라벨<br/>URL Parameter의 lang에 의해 응답 언어가 달라짐 |
| data.items[].tags[].labels[].score | float32              | O  | 0.9545                                                              | 태그 라벨의 신뢰도                                   |
| data.items[].center                | float64 array        | O  | [0.825047801147227, 0.330948979591837]                              | 감지된 아이템의 중앙 x, y 좌표 %                        |
| data.items[].b0                    | float64 array        | O  | [0.676864247418738, 0.219377551020408]                              | 감지된 아이템의 x0, y0 좌표 %                         |
| data.items[].b1                    | float64 array        | O  | [0.973231355525813, 0.4426204081632654]                             | 감지된 아이템의 x1, y1 좌표 %                         |

<br>
<details><summary>응답 본문 예</summary>

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
                "attribute": "category", "labels": [{ "label": "블라우스", "score": 0.9966272115707397 }]
            }, {
                "attribute": "color", "labels": [{ "label": "베이지/아이보리", "score": 0.7692235112190247 }]
            }, {
                "attribute": "pattern", "labels": [{ "label": "무지", "score": 0.9893960356712341 }]
            }, {
                "attribute": "fabric", "labels": [{ "label": "실크", "score": 0.586938738822937 }]
            }, {
                "attribute": "neckline", "labels": [{ "label": "셔츠칼라", "score": 0.9922573566436768 }]
            }, {
                "attribute": "shoulder", "labels": [{ "label": "퍼프/볼륨", "score": 0.5369117856025696 }]
            }, {
                "attribute": "sleeve_length", "labels": [{ "label": "긴소매", "score": 0.6998409032821655 }]
            }, {
                "attribute": "sleeve_shape", "labels": [{ "label": "스트레이트/일자", "score": 0.689109206199646 }]
            }, {
                "attribute": "length_up", "labels": [{ "label": "허리선", "score": 0.9575495719909668 }]
            }, {
                "attribute": "age", "labels": [{ "label": "어른", "score": 0.9985153079032898 }]
            }, {
                "attribute": "gender", "labels": [{ "label": "여성", "score": 0.9960111379623413 }]
            }, {
                "attribute": "detail", "labels": [{ "label": "버튼", "score": 0.9440848231315613 }]
            }, {
                "attribute": "fit", "labels": [{ "label": "기본핏/레귤러핏", "score": 0.789472222328186 }]
            }],
            "center": [ 0.46125, 0.34125 ],
            "b0": [ 0.1875, 0.0175 ],
            "b1": [ 0.735, 0.665 ],
            "score": 0.93118
        }, {
            "type": "SKIRT",
            "tags": [{
                "attribute": "category", "labels": [{ "label": "스커트", "score": 0.9997897744178772 }]
            }, {
                "attribute": "color", "labels": [{ "label": "브라운/갈색", "score": 0.8597127199172974 }]
            }, {
                "attribute": "pattern", "labels": [{ "label": "무지", "score": 0.988312304019928 }]
            }, {
                "attribute": "fabric", "labels": [{ "label": "캔버스", "score": 0.24775846302509308 }]
            }, {
                "attribute": "length_lo", "labels": [{ "label": "숏", "score": 0.9987099170684814 }]
            }, {
                "attribute": "age", "labels": [{ "label": "어른", "score": 0.9993846416473389 }]
            }, {
                "attribute": "gender", "labels": [{ "label": "여성", "score": 0.9950520396232605 }]
            }, {
                "attribute": "detail", "labels": [{ "label": "랩스타일", "score": 0.7058117985725403 }]
            }, {
                "attribute": "fit", "labels": [{ "label": "기본핏/레귤러핏", "score": 0.9844645857810974 }]
            }, {
                "attribute": "shape", "labels": [{ "label": "A라인/플레어", "score": 0.9432026743888855 }]
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

#### 오류 코드

| resultCode | resultMessage               | 설명                                                                           |
|------------|-----------------------------|------------------------------------------------------------------------------|
| -40000     | InvalidParam                | 파라미터에 오류가 있음                                                                 |
| -41000     | UnauthorizedAppKey          | 승인되지 않은 Appkey                                                               |
| -42000     | NotExistService             | 등록되지 않은 서비스                                                                  |
| -45020     | ImageTooLargeException      | 이미지 파일의 크기가 너무 큼<br>[이미지 가이드](#input-image-guide) 참고  |
| -45040     | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[이미지 가이드](#input-image-guide) 참고 |
| -45050     | InvalidImageURLException    | 접근할 수 없는 URL                                                                 |
| -45060     | ImageTimeoutError           | 이미지 다운로드 시간 초과                                                               |
| -50000     | InternalServerError         | 서버 오류                                                                        |

### 색인 요청

* 인덱싱할 데이터를 전달합니다.
* 전달된 파일의 첫 번째 줄을 분석하여 포맷 오류 여부를 검사합니다.
* 첫 번째 줄에서 오류가 발견되지 않으면 색인을 위한 대기열에 들어간 뒤 스케줄에 따라 색인됩니다.

#### 파일 데이터 포맷

| 이름          | field        | value type | 필수 | max length | 비고                                 |
|-------------|--------------|------------|----|------------|------------------------------------|
| 상품ID        | product_id   | string     | O  | 72         | unique key                         |
| 상태          | status       | string     | O  | 7          | enable: 추가 또는 업데이트<br/>disable: 삭제 |
| 상품이름        | name         | string     | O  | 256        | 상품명                                |
| 카테고리 1depth | category1_id | string     | O  | 72         | 카테고리 1depth 아이디                    |
| 카테고리 2depth | category2_id | string     | O  | 72         | 카테고리 2depth 아이디                    |
| 카테고리 3depth | category3_id | string     | O  | 72         | 카테고리 3depth 아이디                    |
| 이미지url      | image_url    | string     | O  | 1000       | 접근 가능한 이미지 URL                     |
| 필터1         | s1           | string     | O  | 72         | 제한 검색을 위한 필터1                      |
| 필터2         | s2           | string     | O  | 72         | 제한 검색을 위한 필터2                      |


##### jsonl 예
```
{"product_id": "10001", "status": "enable", "name": "AAA red onepiece", "category1_id": "1", "category2_id": "1", "category3_id": "2", "image_url": "http://aaaaaaa.bbbbb.jpg", "s1": "1", "s2": "2"}
{"product_id": "10002", "status": "disable", "name": "BBB blue onepiece", "category1_id": "1", "category2_id": "1", "category3_id": "2", "image_url": "http://bbbbbbb.ccccc.jpg", "s1": "s1", "s2": "2"}
{"product_id": "10003", "status": "enable", "name": "BBB blue blouse", "category1_id": "1", "category2_id": "1", "category3_id": "3", "image_url": "http://bbbbbbb.ddddd.jpg", "s1": "", "s2": "s2"}
...
```

##### csv 예
```
10001,enable,AAA red onepiece,1,1,2,http://aaaaaaa.bbbbb.jpg,1,2
10002,disable,BBB blue onepiece,1,1,2,http://bbbbbbb.ccccc.jpg,s1,2
10003,enable,BBB blue blouse,1,1,3,http://bbbbbbb.ddddd.jpg,,s2
...
```

#### 요청
* 직접 데이터 파일을 전송하거나 다운로드 가능한 URL로 데이터 파일을 전달할 수 있습니다.

[URI]

| 메서드  | URI                                                                   |
|------|-----------------------------------------------------------------------|
| POST | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index |

[Path Variable]

| 이름        | 설명                      |
|-----------|-------------------------|
| appKey    | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스명                    |

[URL Parameter]

| 이름     | 타입     | 필수 | 예제    | 설명           |
|--------|--------|----|-------|--------------|
| format | string | O  | jsonl | jsonl 또는 csv |

[Form Data]

| 이름   | 타입     | 필수 여부 | 예제                                                     | 설명                                                     |
|------|--------|-------|--------------------------------------------------------|--------------------------------------------------------|
| link | string | △     | "https://cdn.my-domain.com/202106251000_product.jsonl" | 데이터 파일 URL                                             |
| file | file   | △     | @filename                                              | 데이터 파일<br/>link가 file보다 우선 순위가 높아서 link가 있으면 file은 무시됨 |

<details>
<summary>요청 예 1</summary>

```
curl -X POST "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index?format=jsonl" -H "Content-Type: multipart/form-data" -F "file=@/home/user1/202106251000_product.jsonl"
```

</details>

<details>
<summary>요청 예 2</summary>

```
curl -X POST "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/index?format=jsonl" -F "link=https://cdn.my-domain.com/202106251000_product.jsonl"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
  * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름           | 타입     | 필수 | 예제                                   | 설명     |
|--------------|--------|----|--------------------------------------|--------|
| data.indexID | string | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | 인덱스 ID |

<details>
<summary>응답 본문 예</summary>

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

#### 오류 코드
| resultCode | resultMessage               | 설명                                   |
|------------|-----------------------------|--------------------------------------|
| -40000     | InvalidParam                | 파라미터에 오류가 있음                         |
| -40010     | InvalidFileError            | 파일 전달에 오류가 있는 경우                     |
| -40020     | NoDataError                 | 전달된 파일이 빈 파일인 경우                     |
| -40030     | ExceedDataSizeError         | 전달된 파일이 정해진 용량 또는 정해진 데이터 개수를 초과한 경우 |
| -40080     | TooManyRequestError         | 동시에 여러 번 요청을 한 경우                    |
| -40400     | NoApiFound                  | 정의되지 않은 API로 요청한 경우                  |
| -41000     | UnauthorizedAppKey          | 승인되지 않은 Appkey                       |
| -42000     | NotExistService             | 등록되지 않은 서비스                          |
| -50000     | InternalServerError         | 서버 오류                                |

### 서비스 정보
* 서비스들의 현재 정보를 확인합니다.
  * 서비스별로 남은 색인 횟수
  * 서비스별 색인된 문서 개수

#### 요청

[URI]

| 메서드 | URI                                                  |
|-----|------------------------------------------------------|
| GET | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/services |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |

<details>
<summary>요청 예 </summary>

```
curl -X GET "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/services"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
  * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                           | 타입     | 필수 | 예제    | 설명                  |
|------------------------------|--------|----|-------|---------------------|
| data.totalService            | int    | O  | 3     | 서비스 개수              |
| data.items[].documentCnt     | int    | O  | 51128 | 전체 문서 개수            |
| data.items[].remainInsertCnt | int    | O  | 3     | 서비스 당 색인 요청 가능 횟수   |
| data.items[].service         | string | O  | aaa   | 상품에서 등록한 service_id |

<details>
<summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage       | 설명                  |
|------------|---------------------|---------------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음        |
| -41000     | UnauthorizedAppKey  | 승인되지 않은 Appkey      |
| -50000     | InternalServerError | 서버 오류               |

### 색인 상태 조회
* 요청된 색인들의 현재 상태를 확인합니다.
* 색인 상태의 최대 보관 기간은 등록 시간 기준 6개월입니다.

#### 요청

[URI]

| 메서드 | URI                                                                     |
|-----|-------------------------------------------------------------------------|
| GET | /nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/indexes |

[Path Variable]

| 이름        | 설명                      |
|-----------|-------------------------|
| appKey    | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스명                    |

[URL Parameter]

| 이름     | 타입     | 필수 | 예제                  | 설명                                                                                  |
|--------|--------|----|---------------------|-------------------------------------------------------------------------------------|
| start  | int    | O  | 0                   | 시작 인덱스<br/>0부터 시작                                                                   |
| limit  | int    | O  | 100                 | 최대 100<br/>start:0, limit:100의 경우 1부터 100까지<br/>start:200, limit:100 이면 201부터 300까지 |
| order  | string | X  | "reservedTime:desc" | (기본값)등록 시간 내림 차순<br/>조건 1개만 설정 가능<br/>설정 가능 조건은 ['정렬'](#indexes-status-order) 참조    |
| status | string | X  | "finished"          | 색인의 상탯값                                                                             |

#### Paging
* start와 limit 파라미터로 페이징이 가능합니다.
  * start: 0부터 시작합니다.
  * limit: 0보다 커야 하며 최대 100까지 가능합니다.
* 최대 페이징 가능한 숫자는 1000입니다.
  * 가능:
    * start: 900
    * end: 100
  * 불가능:
    * start: 901
    * end: 100
    * 최대 가능 페이징 수인 1000을 넘어가기 때문에 불가능합니다.

<span id="indexes-status-order"></span>
#### 정렬
* 응답 문서의 정렬 파라미터
* 파라미터 형식.
  * {정렬 가능 항목}:{정렬 방식}
* 정렬 가능 항목
  * reservedTime: 색인 요청 등록 시간
  * startTime: 색인 시작 시간
  * finishTime: 색인 종료 시간
  * addCnt: 추가된 문서 수
  * failCnt: 실패 문서 수
  * deleteCnt: 삭제 문서 수
  * updateCnt: 수정 문서 수
  * totalCnt: 전체 문서 수
* 정렬 방식
  * asc: 오름차순
  * desc: 내림차순

#### 색인 상태
* 색인 상탯값을 조건으로 검색할 수 있습니다.
  * reserved: 대기
  * running: 진행 중
  * failed: 전체 실패
  * finished: 완료(부분 실패 포함)

<details>
<summary>요청 예 </summary>

```
curl -X GET "/nhn-ai-fashion-maker/v1.0/appkeys/{appKey}/service/{serviceID}/indexes?start=0&limit=100&status=running&order=startTime:desc"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
  * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                        | 타입             | 필수 | 예제                                   | 설명                                                                                              |
|---------------------------|----------------|----|--------------------------------------|-------------------------------------------------------------------------------------------------|
| data.total                | int            | O  | 100                                  | 검색된 전체 문서 개수                                                                                    |
| data.items[].service      | String         | O  | testserviceid                        | 해당 색인요청이 발생한 서비스명                                                                               |
| data.items[].id           | String         | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | 색인 ID                                                                                           |
| data.items[].filename     | String         | O  | 202106251000_product.jsonl           | 색인 파일 이름                                                                                        |
| data.items[].status       | string         | O  | reserved                             | 현재 색인 상태를 나타냅니다<br/>reserved: 대기<br/>running: 진행 중<br/>failed: 전체 실패<br/>finished: 완료(부분 실패 포함) |
| data.items[].reservedTime | unix timestamp | O  | 1625098033                           | 색인 등록 시간                                                                                        |
| data.items[].startTime    | unix timestamp | O  | 1625098033                           | 색인 시작 시간                                                                                        |
| data.items[].finishTime   | unix timestamp | O  | 1625098033                           | 색인이 완료된 시간                                                                                      |
| data.items[].addCnt       | Int            | O  | 234                                  | 추가된 문서 개수                                                                                       |
| data.items[].failCnt      | Int            | O  | 31                                   | 실패한 문서 개수<br/>Image Download 실패 등이 포함되며, 패션 아이템을 찾지 못한 경우도 포함.                                  |
| data.items[].deleteCnt    | Int            | O  | 31                                   | 삭제된 문서 개수                                                                                       |
| data.items[].updateCnt    | int            | O  | 592                                  | 수정된 문서 개수                                                                                       |
| data.items[].totalCnt     | Int            | O  | 888                                  | 색인 총 문서 개수                                                                                      |

<details>
<summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistService | 등록되지 않은 서비스 |
| -50000 | InternalServerError | 서버 오류 |
