## AI Service > AI Fashion > API v2.0 가이드

- AI Fashion v2.0 서비스를 사용하는 데 필요한 API를 설명합니다.

## API 공통 정보
### 사전 준비
- API 사용을 위해서는 프로젝트 통합 앱키 또는 서비스 앱키가 필요합니다.
    - 프로젝트 통합 앱키 사용을 권장합니다.
    - 프로젝트 통합 앱키는 프로젝트 설정 페이지의 API 보안 설정에서 생성해 사용할 수 있습니다.
    - 서비스 앱키는 콘솔 상단 **URL & Appkey** 메뉴에서 확인이 가능합니다.

### 요청 공통 정보
- API를 사용하기 위해서는 보안 키 인증 처리가 필요합니다.

[API 도메인]

| 도메인                                       |
|-------------------------------------------|
| https://api-aifashion.nhncloudservice.com |

[Header]

| 이름 | 값 | 설명 |
| --- | --- | --- |
| Authorization | {secretKey} | 콘솔에서 발급 받은 보안 키 |

### 색인 가이드

* 지원되는 파일 형식: JSONL(.jsonl), CSV(.csv)
  * CSV 파일을 작성할 때는 첫 줄부터 실제 데이터로 채워야 합니다.
  * 파일에 빈 줄이 없어야 합니다.
  * 내용은 UTF-8로 인코딩되어야 합니다.
* 색인 파일의 최대 크기: 5MB
* 색인 파일 내 데이터 최대 개수: 10,000개(줄로 구분)
* 이미지 URL 컨텐츠의 최대 크기: 20MB
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
| 이름          | 필드명         |
|-------------|-------------|
| 카테고리 1depth | category1Id |
| 카테고리 2depth | category2Id |
| 카테고리 3depth | category3Id |
| 필터 1        | s1          |
| 필터 2        | s2          |

#### 문법
* `filter.{필드명}`=`연산자`:`값`

| 조건             | 연산자    | 예제                                                                                                                                       | 설명                                                                |
|----------------|--------|------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| equal(default) | equal  | filter.category2Id=1003,1005 or<br/>filter.category2Id=equal:1003 or<br/>filter.category2Id=equal:1003&filter.s1=equal:1                 | 대상 필드의 값이 파라미터 값과 같은 문서만 결과로 응답합니다.<br/>콤마(,)로 구분하여 OR 검색이 가능합니다. |
| not equal      | !equal | filter.category2Id=!equal:1003 or <br/>filter.category2Id=!equal:1003,1005 or<br/>filter.category2Id=!equal:1003,1005&filter.s1=!equal:1 | 대상 필드의 값이 파라미터 값과 다른 문서만 결과로 응답합니다.<br/>콤마(,)로 구분하여 OR 검색이 가능합니다. |

<span id="common-response"></span>
### 응답 공통 정보

- 모든 API 요청에 '200 OK'로 응답합니다. 자세한 응답 결과는 응답 본문 헤더를 참고합니다.

[응답 본문 헤더]

| 이름                   | 타입      | 설명                              |
|----------------------|---------|---------------------------------|
| header.isSuccessful  | boolean | true: 정상<br>false: 오류           |
| header.resultCode    | integer     | 0: 정상<br>0보다 큼: 부분 성공<br>음수: 오류 |
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

### 서비스 생성

* 서비스를 생성할 수 있는 API
* 최대 5개까지 생성 가능
* 허용 문자
    * 영어 소문자, 숫자, '-', '\_' 허용
    * 첫 글자는 영어 소문자만 허용
    * 2~32 글자 허용

#### 요청

[URI]

| 메서드  | URI                             |
|------|---------------------------------|
| POST | /v2.0/appkeys/{appKey}/services |

[Path Variable]

| 이름     | 설명                      |
|--------|-------------------------|
| appKey | 통합 앱키 또는 서비스 앱키 |

[Request Body]

| 이름          | 설명   |
|-------------|------|
| serviceName | 서비스명 |

<details><summary>요청 예</summary>

```
curl -X POST -H "Authorization: {secretKey}" -H "Content-Type: application/json;charset=UTF-8" "${domain}/v2.0/appkeys/{appKey}/services" -d '{
    "serviceName": "my-serviceName"
}'
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

| resultCode | resultMessage                 | 설명                 |
|------------|-------------------------------|--------------------|
| -40000     | InvalidParam                  | 파라미터에 오류가 있음       |
| -41005     | UnauthorizedAppKeyOrSecretKey | 승인되지 않은 앱키 또는 비밀 키 |
| -42010     | DuplicateServiceName          | 중복된 서비스명           |
| -42030     | ServiceQuotaExceededException | 허용된 서비스 개수 초과      |
| -50000     | InternalServerError           | 서버 오류              |

### 서비스 삭제

* 서비스를 삭제할 수 있는 API
* 서비스에 처리 중인 색인 요청이 있는 경우도, 서비스를 삭제할 수 있습니다.

#### 요청

[URI]

| 메서드    | URI                                           |
|--------|-----------------------------------------------|
| DELETE | /v2.0/appkeys/{appKey}/services/{serviceName} |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명                    |

<details><summary>요청 예</summary>

```
curl -X DELETE -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}"
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
| -41005     | UnauthorizedAppKeyOrSecretKey  | 승인되지 않은 앱키 또는 비밀 키 |
| -42000     | NotExistService     | 존재하지 않는 서비스    |
| -50000     | InternalServerError | 서버 오류          |

### 서비스 목록 조회

* 생성한 서비스의 목록을 가져오는 API

#### 요청

[URI]

| 메서드 | URI                             |
|-----|---------------------------------|
| GET | /v2.0/appkeys/{appKey}/services |

[Path Variable]

| 이름     | 설명                      |
|--------|-------------------------|
| appKey | 통합 앱키 또는 서비스 앱키 |

<details><summary>요청 예</summary>

```
curl -X GET -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                             | 타입     | 필수 | 예제         | 설명                |
|--------------------------------|--------|----|------------|-------------------|
| data.totalCount                | integer    | O  | 2          | 총 검색 결과 개수        |
| data.items[].serviceName       | string | O  | my-service | 서비스명              |
| data.items[].documentCount     | string | O  | 100        | 전체 문서 개수          |
| data.items[].remainInsertCount | integer    | O  | 3          | 서비스 당 추가 가능한 문서 수 |

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
                "serviceName": "my-serviceName",
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

#### 오류 코드

| resultCode | resultMessage       | 설명             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음   |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 승인되지 않은 앱키 또는 비밀 키 |
| -50000     | InternalServerError | 서버 오류          |


### 서비스 조회

* 서비스명으로 특정 서비스의 정보를 가져오는 API

#### 요청

[URI]

| 메서드 | URI                                           |
|-----|-----------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName} |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명                    |

<details><summary>요청 예</summary>

```
curl -X GET -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/my-service"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
  * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                     | 타입      | 필수 | 예제         | 설명                |
|------------------------|---------|----|------------|-------------------|
| data.serviceName       | string  | O  | my-service | 서비스명              |
| data.documentCount     | string  | O  | 100        | 전체 문서 개수          |
| data.remainInsertCount | integer | O  | 3          | 서비스 당 추가 가능한 문서 수 |

<details><summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage       | 설명             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음   |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 승인되지 않은 앱키 또는 비밀 키 |
| -50000     | InternalServerError | 서버 오류          |


## 유사 이미지 상품 추천

### 상품 아이디로 유사 상품 검색

* 상품 아이디를 기반으로 유사한 상품을 찾아주는 API

#### 요청

[URI]

| 메서드 | URI                                                                       |
|-----|---------------------------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/products/{productId}/search |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명                    |
| productId   | 상품 아이디                  |

[URL Parameter]

| 이름                 | 타입      | 필수 | 예제       | 설명                                                                                                                                                      |
|--------------------|---------|----|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| limit              | integer | O  | 100      | 최대 크기<br>1 이상 200 이하로 설정 가능                                                                                                                             |
| filter.category1Id | string  | X  | equal:3  | category1Id 값으로 필터링                                                                                                                                     |
| filter.category2Id | string  | X  | !equal:3 | category2Id 값으로 필터링                                                                                                                                     |
| filter.category3Id | string  | X  | !equal:3 | category3Id 값으로 필터링                                                                                                                                     |
| filter.s1          | string  | X  | equal:3  | s1 값으로 필터링                                                                                                                                              |
| filter.s2          | string  | X  | !equal:3 | s2 값으로 필터링                                                                                                                                              |
| minSimilarity      | float   | X  | 0.8      | 매칭 여부를 판단하는 유사도 최소 기준값<br/> data.items[].similarity >= minSimilarity 항목만 매칭되는 것으로 판단합니다.<br/>0 초과 1.0 이하로 설정 가능                                         |
| includeDuplicates  | boolean | X  | false    | 중복 이미지 포함 여부(기본값: false)<br/>중복 이미지 포함 여부가 false인 경우 동일한 상품은 중복 제거가 되므로, 응답 결과가 요청한 문서 수 보다 적을 수 있습니다. 이를 원치 않을 경우 중복 이미지 포함 여부를 true로 설정하여 요청하시기 바랍니다. |

* filter.category1~3_id, filter.s1~2는 [필터링 가이드](#filtering-guide)에서 확인 가능

<details><summary>요청 예</summary>

```
curl -X GET -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/products/{productId}/search?limit=100&filter.s1=equal:1"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능
  
[응답 본문 데이터]

| 이름                       | 타입      | 필수 | 예제                           | 설명              |
|--------------------------|---------|----|------------------------------|-----------------|
| data.totalCount          | integer | O  | 100                          | 총 검색 결과 개수      |
| data.query               | string  | O  | productId=10234455&limit=100 | 검색 질의           |
| data.items[].similarity  | float   | O  | 0.91234                      | 검색 유사도 점수       |
| data.items[].productId   | string  | O  | 8980335                      | 상품 아이디          |
| data.items[].name        | string  | O  | AAA red onepiece             | 상품명             |
| data.items[].imageUrl    | string  | O  | http://url.com               | 접근 가능한 이미지 URL  |
| data.items[].category1Id | string  | X  | 72                           | 카테고리 1depth 아이디 |
| data.items[].category2Id | string  | X  | 72                           | 카테고리 2depth 아이디 |
| data.items[].category3Id | string  | X  | 72                           | 카테고리 3depth 아이디 |
| data.items[].s1          | string  | X  | 72                           | 제한 검색을 위한 필터1   |
| data.items[].s2          | string  | X  | 72                           | 제한 검색을 위한 필터2   |


<details><summary>응답 본문 예</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 1,
        "query": "productId=10234455&limit=100",
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

#### 오류 코드

| resultCode | resultMessage       | 설명             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음   |
| -40050     | NotFoundProductId   | 상품 아이디가 없음     |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 승인되지 않은 앱키 또는 비밀 키 |
| -42000     | NotExistService     | 존재하지 않는 서비스    |
| -50000     | InternalServerError | 서버 오류          |


## 카메라 검색

### 패션 아이템 감지

* 입력된 이미지에서 패션 아이템을 감지하는 API

#### 요청

[URI]

| 메서드 | URI                                                  |
|-----|------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/detect |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명                    |

[URL Parameter]

| 이름   | 타입     | 필수 | 예제                                        | 설명                     |
|------|--------|----|-------------------------------------------|------------------------|
| path | string | O  | `https://imagecdn.co.kr/sample_image.jpg` | URL 인코딩된 이미지의 URL |

<details><summary>요청 예</summary>

```
curl -X GET -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/detect?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                  | 타입           | 필수 | 예제                                               | 설명                                                      |
|---------------------|--------------|----|--------------------------------------------------|---------------------------------------------------------|
| data.totalCount     | integer      | O  | 100                                              | 총 검색 결과 개수                                              |
| data.query          | string       | O  | `path=https://imagecdn.co.kr/sample_image.jpg`   | 검색 질의                                                   |
| data.items[].link   | string       | O  | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | [감지된 패션 아이템으로 유사 상품 검색](#search-by-detect-link)에서 사용할 값 |
| data.items[].center | double array | O  | [0.825047801147227, 0.330948979591837]           | 감지된 아이템의 중앙 x, y 좌표 %                                   |
| data.items[].b0     | double array | O  | [0.676864247418738, 0.219377551020408]           | 감지된 아이템의 x0, y0 좌표 %                                    |
| data.items[].b1     | double array | O  | [0.973231355525813, 0.4426204081632654]          | 감지된 아이템의 x1, y1 좌표 %                                    |
| data.items[].score  | float        | O  | 0.9732                                           | 감지된 아이템의 신뢰도                                            |

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

| resultCode | resultMessage               | 설명                                                    |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | 파라미터에 오류가 있음                                          |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 승인되지 않은 앱키 또는 비밀 키                                        |
| -42000     | NotExistService             | 존재하지 않는 서비스                                           |
| -45020     | ImageTooLargeException      | 이미지 파일의 크기가 너무 큼<br>[이미지 가이드](#input-image-guide) 참고  |
| -45040     | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[이미지 가이드](#input-image-guide) 참고 |
| -45050     | InvalidImageURLException    | 접근할 수 없는 URL                                          |
| -45060     | ImageTimeoutError           | 이미지 다운로드 시간 초과                                        |
| -50000     | InternalServerError         | 서버 오류                                                 |

<span id="search-by-detect-link"></span>
### 감지된 패션 아이템으로 유사 상품 검색

* detect API에서 응답으로 받은 link를 기반으로 유사한 상품을 찾아주는 API

#### 요청

[URI]

| 메서드 | URI                                                  |
|-----|------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/search |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스 아이디                 |

[URL Parameter]

| 이름                 | 타입      | 필수 | 예제                                                 | 설명                                                                                                                                                      |
|--------------------|---------|----|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| limit              | integer | O  | 100                                                | 최대 크기<br>1 이상 200 이하로 설정 가능                                                                                                                             |
| link               | string  | O  | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0%3D | detect API에서 전달받은 link(URL 인코딩 필요)                                                                                                                      |
| filter.category1Id | string  | X  | equal:3                                            | category1Id 값으로 필터링                                                                                                                                     |
| filter.category2Id | string  | X  | !equal:3                                           | category2Id 값으로 필터링                                                                                                                                     |
| filter.category3Id | string  | X  | !equal:3                                           | category3Id 값으로 필터링                                                                                                                                     |
| filter.s1          | string  | X  | equal:3                                            | s1 값으로 필터링                                                                                                                                              |
| filter.s2          | string  | X  | !equal:3                                           | s2 값으로 필터링                                                                                                                                              |
| minSimilarity      | float   | X  | 0.8                                                | 매칭 여부를 판단하는 유사도 최소 기준값<br/> data.items[].similarity >= minSimilarity 항목만 매칭되는 것으로 판단합니다.<br/>0 초과 1.0 이하로 설정 가능                                         |
| includeDuplicates  | boolean | X  | false                                              | 중복 이미지 포함 여부(기본값: false)<br/>중복 이미지 포함 여부가 false인 경우 동일한 상품은 중복 제거가 되므로, 응답 결과가 요청한 문서 수 보다 적을 수 있습니다. 이를 원치 않을 경우 중복 이미지 포함 여부를 true로 설정하여 요청하시기 바랍니다. |

* filter.category1~3_id, filter.s1~2는 [필터링 가이드](#filtering-guide)에서 확인 가능

<details><summary>요청 예</summary>

```
curl -X GET -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/image?limit=100&link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0%3D&filter.s1=equal:1"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                       | 타입      | 필수 | 예제                                                              | 설명              |
|--------------------------|---------|----|-----------------------------------------------------------------|-----------------|
| data.totalCount          | integer | O  | 100                                                             | 총 검색 결과 개수      |
| data.query               | string  | O  | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | 검색 질의           |
| data.items[].similarity  | float   | O  | 0.91234                                                         | 검색 유사도 점수       |
| data.items[].productId   | string  | O  | 8980335                                                         | 상품 아이디          |
| data.items[].name        | string  | O  | AAA red onepiece                                                | 상품명             |
| data.items[].imageUrl    | string  | O  | http://url.com                                                  | 접근 가능한 이미지 URL  |
| data.items[].category1Id | string  | X  | 72                                                              | 카테고리 1depth 아이디 |
| data.items[].category2Id | string  | X  | 72                                                              | 카테고리 2depth 아이디 |
| data.items[].category3Id | string  | X  | 72                                                              | 카테고리 3depth 아이디 |
| data.items[].s1          | string  | X  | 72                                                              | 제한 검색을 위한 필터1   |
| data.items[].s2          | string  | X  | 72                                                              | 제한 검색을 위한 필터2   |


<details><summary>응답 본문 예</summary>

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

#### 오류 코드

| resultCode | resultMessage               | 설명                                                    |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | 파라미터에 오류가 있음                                          |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 승인되지 않은 앱키 또는 비밀 키                                        |
| -42000     | NotExistService             | 존재하지 않는 서비스                                           |
| -45020     | ImageTooLargeException      | 이미지 파일의 크기가 너무 큼<br>[이미지 가이드](#input-image-guide) 참고  |
| -45040     | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[이미지 가이드](#input-image-guide) 참고 |
| -45050     | InvalidImageURLException    | 접근할 수 없는 URL                                          |
| -45060     | ImageTimeoutError           | 이미지 다운로드 시간 초과                                        |
| -45070     | NoDetectedFashionItems      | 감지된 패션 아이템 없음                                        |
| -50000     | InternalServerError         | 서버 오류                                                 |

### 이미지로 유사 상품 검색

* 이미지(URL, File)에서 인식된 가장 큰 패션 아이템을 기준으로 유사한 상품을 찾아주는 API
* filter.category1~3_id, filter.s1~2는 [필터링 가이드](#filtering-guide)에서 확인 가능
* Content-Type: multipart/form-data

#### 요청

[URI]

| 메서드  | URI                                                  |
|------|------------------------------------------------------|
| POST | /v2.0/appkeys/{appKey}/services/{serviceName}/search |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명                    |

[Request Body]

| 이름                 | 타입      | 필수 | 예제              | 설명                                                                                                                                                      |
|--------------------|---------|----|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| imageUrl           | string  | △  | http://url.com	 | 이미지의 URL                                                                                                                                                |
| imageFile          | file    | △  | image.png	      | 이미지 파일                                                                                                                                                  |
| limit              | integer | O  | 100             | 최대 크기<br>1 이상 200 이하로 설정 가능                                                                                                                             |
| filter.category1Id | string  | X  | equal:3         | category1Id 값으로 필터링                                                                                                                                     |
| filter.category2Id | string  | X  | !equal:3        | category2Id 값으로 필터링                                                                                                                                     |
| filter.category3Id | string  | X  | !equal:3        | category3Id 값으로 필터링                                                                                                                                     |
| filter.s1          | string  | X  | equal:3         | s1 값으로 필터링                                                                                                                                              |
| filter.s2          | string  | X  | !equal:3        | s2 값으로 필터링                                                                                                                                              |
| minSimilarity      | float   | X  | 0.8             | 매칭 여부를 판단하는 유사도 최소 기준값<br/> data.items[].similarity >= minSimilarity 항목만 매칭되는 것으로 판단합니다.<br/>0 초과 1.0 이하로 설정 가능                                         |
| includeDuplicates  | boolean | X  | false           | 중복 이미지 포함 여부(기본값: false)<br/>중복 이미지 포함 여부가 false인 경우 동일한 상품은 중복 제거가 되므로, 응답 결과가 요청한 문서 수 보다 적을 수 있습니다. 이를 원치 않을 경우 중복 이미지 포함 여부를 true로 설정하여 요청하시기 바랍니다. |

<details><summary>요청 예</summary>

```
curl -X POST -H "Authorization: {secretKey}" -H "Content-Type: multipart/form-data" -F imageFile=@image.png -F limit=100 "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/search"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
  * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                       | 타입      | 필수 | 예제                           | 설명              |
|--------------------------|---------|----|------------------------------|-----------------|
| data.totalCount          | integer | O  | 100                          | 총 검색 결과 개수      |
| data.items[].productId   | string  | O  | 8980335                      | 상품 아이디          |
| data.items[].similarity  | float   | O  | 0.91234                      | 검색 유사도 점수       |
| data.items[].name        | string  | O  | AAA red onepiece             | 상품명             |
| data.items[].imageUrl    | string  | O  | http://url.com               | 접근 가능한 이미지 URL  |
| data.items[].category1Id | string  | X  | 72                           | 카테고리 1depth 아이디 |
| data.items[].category2Id | string  | X  | 72                           | 카테고리 2depth 아이디 |
| data.items[].category3Id | string  | X  | 72                           | 카테고리 3depth 아이디 |
| data.items[].s1          | string  | X  | 72                           | 제한 검색을 위한 필터1   |
| data.items[].s2          | string  | X  | 72                           | 제한 검색을 위한 필터2   |


<details><summary>응답 본문 예</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 1,
        "query": "productId=10234455&limit=100",
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

#### 오류 코드

| resultCode | resultMessage               | 설명                                                    |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | 파라미터에 오류가 있음                                          |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 승인되지 않은 앱키 또는 비밀 키                                        |
| -42000     | NotExistService             | 존재하지 않는 서비스                                           |
| -45020     | ImageTooLargeException      | 이미지 파일의 크기가 너무 큼<br>[이미지 가이드](#input-image-guide) 참고  |
| -45040     | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[이미지 가이드](#input-image-guide) 참고 |
| -45050     | InvalidImageURLException    | 접근할 수 없는 URL                                          |
| -45060     | ImageTimeoutError           | 이미지 다운로드 시간 초과                                        |
| -45070     | NoDetectedFashionItems      | 감지된 패션 아이템 없음                                         |
| -50000     | InternalServerError         | 서버 오류                                                 |


## 딥 태깅

<span id="tag-api"></span>
### 패션 아이템 태그 감지

* 입력 이미지에서 패션 아이템의 태그 정보를 감지하는 API

#### 요청

[URI]

| 메서드 | URI                                               |
|-----|---------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/tag |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명                    |

[URL Parameter]

| 이름        | 타입      | 필수 | 예제                                        | 설명                                                                                               |
|-----------|---------|----|-------------------------------------------|--------------------------------------------------------------------------------------------------|
| path      | string  | O  | `https://imagecdn.co.kr/sample_image.jpg` | URL 인코딩된 이미지 URL                                                                                 |
| lang      | string  | X  | ko                                        | 라벨의 언어(기본값: en)<br/>en: English<br/>ko: Korean<br/>ja: Japanese                                  |
| itemLimit | integer | X  | 3                                         | 이미지에서 발견된 패션 아이템 중 태그 정보를 응답할 아이템 숫자<br/>아이템의 너비가 긴 순서로 정렬(기본값: 1)<br/>최대 크기<br>1 이상 4 이하로 설정 가능 |

<details><summary>요청 예</summary>

```
curl -X GET -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/tag?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=ko&item_limit=3"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                                 | 타입           | 필수 | 예제                                                                  | 설명                                           |
|------------------------------------|--------------|----|---------------------------------------------------------------------|----------------------------------------------|
| data.totalCount                    | integer      | O  | 2                                                                   | 총 검색 결과 개수                                   |
| data.query                         | string       | O  | `path=https://imagecdn.co.kr/sample_image.jpg&lang=ko&item_limit=3` | 검색 질의                                        |
| data.items[].type                  | string       | O  | JACKET                                                              | 감지된 아이템의 유형                                |
| data.items[].score                 | float        | O  | 0.9515                                                              | 감지된 아이템의 신뢰도                                 |
| data.items[].tags                  | array        | O  |                                                                     | 감지된 아이템 태그 정보의 배열                            |
| data.items[].tags[].attribute      | string       | O  | category                                                            | 태그의 속성                                       |
| data.items[].tags[].labels         | array        | O  |                                                                     | 태그 라벨의 배열                                    |
| data.items[].tags[].labels[].label | string       | O  | 블라우스 \| Blouse                                                      | 태그 라벨<br/>URL 파라미터의 lang에 의해 응답 언어가 달라짐 |
| data.items[].tags[].labels[].score | float        | O  | 0.9545                                                              | 태그 라벨의 신뢰도                                   |
| data.items[].center                | double array | O  | [0.825047801147227, 0.330948979591837]                              | 감지된 아이템의 중앙 x, y 좌표 %                        |
| data.items[].b0                    | double array | O  | [0.676864247418738, 0.219377551020408]                              | 감지된 아이템의 x0, y0 좌표 %                         |
| data.items[].b1                    | double array | O  | [0.973231355525813, 0.4426204081632654]                             | 감지된 아이템의 x1, y1 좌표 %                         |

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

| resultCode | resultMessage               | 설명                                                    |
|------------|-----------------------------|-------------------------------------------------------|
| -40000     | InvalidParam                | 파라미터에 오류가 있음                                          |
| -41005     | UnauthorizedAppKeyOrSecretKey          | 승인되지 않은 앱키 또는 비밀 키                                        |
| -42000     | NotExistService             | 존재하지 않는 서비스                                           |
| -45020     | ImageTooLargeException      | 이미지 파일의 크기가 너무 큼<br>[이미지 가이드](#input-image-guide) 참고  |
| -45040     | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[이미지 가이드](#input-image-guide) 참고 |
| -45050     | InvalidImageURLException    | 접근할 수 없는 URL                                          |
| -45060     | ImageTimeoutError           | 이미지 다운로드 시간 초과                                        |
| -50000     | InternalServerError         | 서버 오류                                                 |


## 색인
### 색인 요청

* 패션 아이템 데이터를 색인하는 API
* 전달된 파일의 첫 번째 줄을 분석하여 포맷 오류 여부를 검사합니다.
* 첫 번째 줄에서 오류가 발견되지 않으면 색인을 위한 대기열에 들어간 뒤 스케줄에 따라 색인됩니다.
* 직접 데이터 파일을 전송하거나 다운로드 가능한 URL로 데이터 파일을 전달할 수 있습니다.
* Content-Type: multipart/form-data

#### 파일 데이터 포맷

| 이름          | field       | value type | 필수 | max length | 비고                                 |
|-------------|-------------|------------|----|------------|------------------------------------|
| 상품ID        | productId   | string     | O  | 72         | 유니크 키                         |
| 상태          | status      | string     | O  | 7          | enable: 추가 또는 업데이트<br/>disable: 삭제 |
| 상품이름        | name        | string     | O  | 256        | 상품명                                |
| 카테고리 1depth | category1Id | string     | O  | 72         | 카테고리 1depth 아이디                    |
| 카테고리 2depth | category2Id | string     | O  | 72         | 카테고리 2depth 아이디                    |
| 카테고리 3depth | category3Id | string     | O  | 72         | 카테고리 3depth 아이디                    |
| 이미지url      | imageUrl    | string     | O  | 1000       | 접근 가능한 이미지 URL                     |
| 필터1         | s1          | string     | O  | 72         | 제한 검색을 위한 필터1                      |
| 필터2         | s2          | string     | O  | 72         | 제한 검색을 위한 필터2                      |

##### JSONL 예
```
{"productId": "10001", "status": "enable", "name": "AAA red onepiece", "category1Id": "1", "category2Id": "1", "category3Id": "2", "imageUrl": "http://aaaaaaa.bbbbb.jpg", "s1": "1", "s2": "2"}
{"productId": "10002", "status": "disable", "name": "BBB blue onepiece", "category1Id": "1", "category2Id": "1", "category3Id": "2", "imageUrl": "http://bbbbbbb.ccccc.jpg", "s1": "s1", "s2": "2"}
{"productId": "10003", "status": "enable", "name": "BBB blue blouse", "category1Id": "1", "category2Id": "1", "category3Id": "3", "imageUrl": "http://bbbbbbb.ddddd.jpg", "s1": "", "s2": "s2"}
...
```

##### CSV 예
```
10001,enable,AAA red onepiece,1,1,2,http://aaaaaaa.bbbbb.jpg,1,2
10002,disable,BBB blue onepiece,1,1,2,http://bbbbbbb.ccccc.jpg,s1,2
10003,enable,BBB blue blouse,1,1,3,http://bbbbbbb.ddddd.jpg,,s2
...
```

#### 요청

[URI]

| 메서드  | URI                                                   |
|------|-------------------------------------------------------|
| POST | /v2.0/appkeys/{appKey}/services/{serviceName}/indexes |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명                    |

[Form Data]

| 이름     | 타입     | 필수 여부 | 예제                                                   | 설명                                                     |
|--------|--------|-------|------------------------------------------------------|--------------------------------------------------------|
| format | string | O     | jsonl                                                | jsonl 또는 csv                                           |
| link   | string | △     | https://cdn.my-domain.com/202106251000_product.jsonl | 데이터 파일 URL                                             |
| file   | file   | △     | @filename                                            | 데이터 파일<br/>link가 file보다 우선순위가 높아서 link가 있으면 file은 무시됨 |


<details>
<summary>요청 예 1</summary>

```
curl -X POST -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/indexes" -H "Content-Type: multipart/form-data" -F "file=@/home/user1/202106251000_product.jsonl -F format=jsonl"
```

</details>

<details>
<summary>요청 예 2</summary>

```
curl -X POST "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/indexes" -H "Content-Type: multipart/form-data" -F "link=https://cdn.my-domain.com/202106251000_product.jsonl -F format=jsonl"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
  * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름           | 타입     | 필수 | 예제                                   | 설명     |
|--------------|--------|----|--------------------------------------|--------|
| data.indexId | string | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | 인덱스 ID |

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
        "indexId": "24bb94b3-8a6b-488e-b038-4f6038da2596"
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
| -41005     | UnauthorizedAppKeyOrSecretKey          | 승인되지 않은 앱키 또는 비밀 키                       |
| -42000     | NotExistService             | 존재하지 않는 서비스                          |
| -50000     | InternalServerError         | 서버 오류                                |


### 색인 목록 조회
* 요청된 색인 목록을 가져오는 API
* 색인 요청에 대한 정보를 알 수 있습니다.
* 색인 정보의 최대 보관 기간은 등록 시간 기준 6개월입니다.

#### 요청

[URI]

| 메서드 | URI                                                   |
|-----|-------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/indexes |

[Path Variable]

| 이름          | 설명              |
|-------------|-----------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명            |

[URL Parameter]

| 이름       | 타입           | 필수 | 예제                 | 설명                                                                                    |
|----------|--------------|----|--------------------|---------------------------------------------------------------------------------------|
| start    | integer      | O  | 0                  | 시작 인덱스<br/>0부터 시작                                                                     |
| limit    | integer      | O  | 100                | 최대 100<br/>start:0, limit: 100의 경우 1부터 100까지<br/>start: 200, limit: 100 이면 201부터 300까지 |
| order    | string       | X  | requestedTime:desc | 정렬 조건(기본값: requestedTime:desc)<br/>설정 가능 조건은 [정렬](#indexes-status-order) 참조           |
| statuses | string array | X  | running,finished   | 색인의 상태 값                                                                              |

#### 페이징
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
* 파라미터 형식
  * {정렬 가능 항목}:{정렬 방식}
* 정렬 가능 항목
  * requestedTime: 색인 요청 등록 시간
  * startTime: 색인 시작 시간
  * finishTime: 색인 종료 시간
  * addedCount: 추가된 문서 수
  * failedCount: 실패 문서 수
  * deletedCount: 삭제 문서 수
  * exceededCount: 제한된 사용량을 넘어선 문서 수
  * updatedCount: 수정 문서 수
  * totalCount: 전체 문서 수
* 정렬 방식
  * asc: 오름차순
  * desc: 내림차순

#### 색인 상태
* 색인 상태 값을 조건으로 검색할 수 있습니다.
  * reserved: 대기
  * running: 진행 중
  * failed: 전체 실패
  * finished: 완료(부분 실패 포함)
  * canceled: 취소
  * deleted: 삭제

<details>
<summary>요청 예 </summary>

```
curl -X GET -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/indexes?start=0&limit=100&status=running,finished&order=startTime:desc"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
  * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                         | 타입             | 필수 | 예제                                   | 설명                                                                                              |
|----------------------------|----------------|----|--------------------------------------|-------------------------------------------------------------------------------------------------|
| data.total                 | integer        | O  | 100                                  | 검색된 전체 문서 개수                                                                                    |
| data.items[].service       | string         | O  | test                                 | 서비스명                                                                                            |
| data.items[].id            | string         | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | 색인 ID                                                                                           |
| data.items[].filename      | string         | O  | 202106251000_product.jsonl           | 색인 파일 이름                                                                                        |
| data.items[].status        | string         | O  | reserved                             | 현재 색인 상태를 나타냅니다.<br/>reserved: 대기<br/>running: 진행 중<br/>failed: 전체 실패<br/>finished: 완료(부분 실패 포함) |
| data.items[].requestedTime | unix timestamp | O  | 1625098033                           | 색인 요청 등록 시간                                                                                     |
| data.items[].startTime     | unix timestamp | O  | 1625098033                           | 색인 시작 시간                                                                                        |
| data.items[].finishTime    | unix timestamp | O  | 1625098033                           | 색인이 완료된 시간                                                                                      |
| data.items[].addedCount    | integer        | O  | 234                                  | 추가된 문서 개수                                                                                       |
| data.items[].failedCount   | integer        | O  | 31                                   | 실패한 문서 개수<br/>이미지 다운로드 실패 등이 포함되며, 패션 아이템을 찾지 못한 경우도 포함.                                  |
| data.items[].exceededCount | integer        | O  | 31                                   | 제한 사용량을 넘어서 색인할 수 없는 문서 개수                                                                     |
| data.items[].deletedCount  | integer        | O  | 31                                   | 삭제된 문서 개수                                                                                       |
| data.items[].updatedCount  | integer        | O  | 592                                  | 수정된 문서 개수                                                                                       |
| data.items[].totalCount    | integer        | O  | 888                                  | 색인 총 문서 개수                                                                                      |

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

#### 오류 코드

| resultCode | resultMessage       | 설명             |
|------------|---------------------|----------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음   |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 승인되지 않은 앱키 또는 비밀 키 |
| -42000     | NotExistService     | 존재하지 않는 서비스    |
| -50000     | InternalServerError | 서버 오류          |


### 색인 상세 조회
* 특정 색인 요청에 대한 상세 정보를 알 수 있는 API
* 색인 상세 정보의 최대 보관 기간은 등록 시간 기준 6개월입니다.

#### 요청

[URI]

| 메서드 | URI                                                             |
|-----|-----------------------------------------------------------------|
| GET | /v2.0/appkeys/{appKey}/services/{serviceName}/indexes/{indexId} |

[Path Variable]

| 이름          | 설명                      |
|-------------|-------------------------|
| appKey      | 통합 앱키 또는 서비스 앱키 |
| serviceName | 서비스명                    |
| indexId     | 색인 ID                   |

<details>
<summary>요청 예 </summary>

```
curl -X GET -H "Authorization: {secretKey}" "${domain}/v2.0/appkeys/{appKey}/services/{serviceName}/indexes/{indexId}"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
  * [응답 공통 정보](#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름                              | 타입             | 필수 | 예제                                   | 설명                                                                                               |
|---------------------------------|----------------|----|--------------------------------------|--------------------------------------------------------------------------------------------------|
| data.total                      | integer        | O  | 100                                  | 검색된 전체 문서 개수                                                                                     |
| data.items[].serviceName        | string         | O  | test                                 | 서비스명                                                                                             |
| data.items[].id                 | string         | O  | 24bb94b3-8a6b-488e-b038-4f6038da2596 | 색인 ID                                                                                            |
| data.items[].filename           | string         | O  | 202106251000_product.jsonl           | 색인 파일 이름                                                                                         |
| data.items[].status             | string         | O  | reserved                             | 현재 색인 상태를 나타냅니다.<br/>reserved: 대기<br/>running: 진행 중<br/>failed: 전체 실패<br/>finished: 완료(부분 실패 포함) |
| data.items[].requestedTime      | unix timestamp | O  | 1625098033                           | 색인 요청 등록 시간                                                                                      |
| data.items[].startTime          | unix timestamp | O  | 1625098033                           | 색인 시작 시간                                                                                         |
| data.items[].finishTime         | unix timestamp | O  | 1625098033                           | 색인이 완료된 시간                                                                                       |
| data.items[].addedCount         | integer        | O  | 234                                  | 추가된 문서 개수                                                                                        |
| data.items[].addedProductIds    | string array   | O  | [10001, 10002]                       | 추가된 문서 ID 목록                                                                                     |
| data.items[].failedCount        | integer        | O  | 31                                   | 실패한 문서 개수<br/>이미지 다운로드 실패 등이 포함되며, 패션 아이템을 찾지 못한 경우도 포함.                                         |
| data.items[].failedProductIds   | string array   | O  | [10001, 10002]                       | 실패한 문서 ID 목록                                                                                     |
| data.items[].exceededCount      | integer        | O  | 31                                   | 제한 사용량을 넘어서 색인할 수 없는 문서 개수                                                                       |
| data.items[].exceededProductIds | string array   | O  | [10001, 10002]                       | 제한 사용량을 넘어서 색인할 수 없는 문서 ID 목록                                                                    |
| data.items[].deletedCount       | integer        | O  | 31                                   | 삭제된 문서 개수                                                                                        |
| data.items[].deletedProductIds  | string array   | O  | [10001, 10002]                       | 삭제된 문서 ID 목록                                                                                     |
| data.items[].updatedCount       | integer        | O  | 592                                  | 수정된 문서 개수                                                                                        |
| data.items[].updatedProductIds  | string array   | O  | [10001, 10002]                       | 수정된 문서 ID 목록                                                                                     |
| data.items[].totalCount         | integer        | O  | 888                                  | 색인 총 문서 개수                                                                                       |

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

#### 오류 코드

| resultCode | resultMessage       | 설명                  |
|------------|---------------------|---------------------|
| -40000     | InvalidParam        | 파라미터에 오류가 있음        |
| -41005     | UnauthorizedAppKeyOrSecretKey  | 승인되지 않은 앱키 또는 비밀 키      |
| -42000     | NotExistService     | 존재하지 않는 서비스         |
| -50000     | InternalServerError | 서버 오류               |
