
## AI Service > AI Fashion > Service API 가이드

- AI Fashion 검색 서비스를 사용하는 데 필요한 API를 설명합니다.

## API 공통 정보
### 사전 준비
- API 사용을 위해서는 프로젝트 통합 Appkey 또는 서비스 Appkey가 필요합니다.
    - 프로젝트 통합 Appkey 사용을 권장합니다.
    - 프로젝트 통합 Appkey는 프로젝트 설정 페이지의 API 보안 설정에서 생성해 사용할 수 있습니다.
    - 서비스 Appkey는 콘솔 상단 **URL & Appkey** 메뉴에서 확인이 가능합니다.

### 요청 공통 정보
- API를 사용하기 위해서는 보안 키 인증 처리가 필요합니다.

[API 도메인]

| 리전 | 도메인 |
| --- | --- |
| 한국(판교) | https://kr1-aifashion.api.nhncloudservice.com |
| 한국(평촌) | https://kr2-aifashion.api.nhncloudservice.com |
| 한국(광주) | https://kr3-aifashion.api.nhncloudservice.com |
| 일본(도쿄) | https://jp1-aifashion.api.nhncloudservice.com |
| 미국(캘리포니아) | https://us1-api-aifashion.nhncloudservice.com |

<span id="input-image-guide"></span>
### 입력 이미지 가이드

* 입력 이미지 다운로드에 3초 이상 걸리면 실패로 간주합니다.
* 입력 이미지 패션 아이템의 너비와 높이가 모두 20px 이하인 경우에는 인식하지 않습니다.
* 이미지 크기가 커질수록 패션 아이템의 크기도 커져야 더 정확하게 인식할 수 있습니다.
* 이미지에서 패션 아이템이 차지하는 비중이 클수록 더 정확하게 인식할 수 있습니다.
* 이미지 파일의 최대 크기: 5,000,000 bytes(단, [tag API](#tag-api)는 1,000,000 bytes)
* 지원 이미지 포맷: PNG, JPEG, GIF
* 이미지 URL에 포트를 직접 지정하는 경우 80, 443, 10000~12000 포트만 사용 가능합니다.

<span id="filtering-guide"></span>
### 필터링 가이드

* 필터링 기능은 카테고리 또는 필터를 제한하여 검색할 때 사용합니다.

#### 대상 필드
| 이름 | 필드명 |
| --- | --- |
| 카테고리 1depth | category1_id |
| 카테고리 2depth | category2_id |
| 카테고리 3depth | category3_id |
| 필터 1 | s1 |
| 필터 2 | s2 |

#### 문법
* `filter.{필드명}`=`연산자`:`값`

| 조건 | 연산자 | 예제 | 설명 |
| --- | --- | --- | --- |
| equal(default) | equal | filter.category2_id=1003,1005 or<br/>filter.category2_id=equal:1003 or<br/>filter.category2_id=equal:1003&filter.s1=equal:1 | 대상 필드의 값이 파라미터 값과 같은 문서만 결과로 응답합니다.<br/>콤마(,)로 구분하여 OR 검색이 가능합니다. |
| not equal | !equal | filter.category2_id=!equal:1003 or <br/>filter.category2_id=!equal:1003,1005 or<br/>filter.category2_id=!equal:1003,1005&filter.s1=!equal:1 | 대상 필드의 값이 파라미터 값과 다른 문서만 결과로 응답합니다.<br/>콤마(,)로 구분하여 OR 검색이 가능합니다. |

<span id="common-response"></span>
### 응답 공통 정보

- 모든 API 요청에 '200 OK'로 응답합니다. 자세한 응답 결과는 응답 본문 헤더를 참고합니다.

[응답 본문 헤더]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | boolean | true: 정상<br>false: 오류 |
| header.resultCode | int | 0: 정상<br>0보다 큼: 부분 성공<br>음수: 오류 |
| header.resultMessage | string | "SUCCESS": 정상<br>그 외: 오류 메시지 반환 |

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

### 서비스 아이디 등록

* 서비스 아이디를 등록할 수 있는 API
* 최대 5개까지 등록 가능
* 허용 문자
    * 영어 소문자, 숫자, '-', '\_' 허용
    * 2~32 글자 허용
    * 첫 글자는 영어 소문자만 허용

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| POST | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스 아이디 |

<details><summary>요청 예</summary>

```
curl -X POST "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42010 | DuplicateServiceID | 중복된 서비스 아이디 |
| -42030 | ServiceIDQuotaExceededException | 허용된 서비스 아이디 개수 초과 |
| -50000 | InternalServerError | 서버 오류 |

### 서비스 아이디 삭제

* 등록된 서비스 아이디를 삭제할 수 있는 API
* 서비스 아이디에 처리중인 색인 요청이 있는 경우, 서비스 아이디를 삭제할 수 없습니다.

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| DELETE | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스 아이디 |

<details><summary>요청 예</summary>

```
curl -X DELETE "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록되지 않은 서비스 아이디 |
| -42020 | CannotDeletedOnIndexRunning | 색인 중에는 삭제할 수 없음 |
| -50000 | InternalServerError | 서버 오류 |

### 서비스 아이디 목록

* 등록된 서비스 아이디 목록을 확인할 수 있는 API

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/services |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/services"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 5 | 총 검색 결과 개수 |
| data.items[].serviceID | String | O | my-service | 서비스 아이디 |

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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -50000 | InternalServerError | 서버 오류 |

## 유사 이미지 상품 추천

### Search By ProductID

* 상품 아이디를 기반으로 유사한 패션 아이템을 포함한 상품을 찾아주는 API

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID} |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스 아이디 |
| productID | 상품 아이디 |

[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | 최대 크기<br>1 이상 200 이하로 설정 가능 |
| filter.category1_id | string | X | equal:3 | category1_id 값으로 필터링 |
| filter.category2_id | string | X | !equal:3 | category2_id 값으로 필터링 |
| filter.category3_id | string | X | !equal:3 | category3_id 값으로 필터링 |
| filter.s1 | string | X | equal:3 | s1 값으로 필터링 |
| filter.s2 | string | X | !equal:3 | s2 값으로 필터링 |
| threshold | float32 | X | 0.8 | 매칭 여부를 판단하는 유사도 기준값<br/> data.items[].similarity >= threshold인 항목만 매칭되는 것으로 판단합니다.<br/>0 초과 1.0 이하로 설정 가능 |

* filter.category1~3_id, filter.s1~2는 [필터링 가이드](./service-api-guide/#filtering-guide)에서 확인 가능

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID}?limit=100&filter.s1=equal:1"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | 총 검색 결과 개수 |
| data.query | String | O | productID=10234455&limit=100 | 검색 질의 |
| data.items[].similarity | Number | O | 0.91234 | 검색 유사도 점수 |
| data.items[].productID | String | O | 8980335 | 상품 아이디 |

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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -40050 | NotFoundProductIDError | 상품 아이디가 없음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록되지 않은 서비스 아이디 |
| -50000 | InternalServerError | 서버 오류 |

## 카메라 검색

### Detect

* 입력 이미지에서 패션 아이템을 감지하는 API입니다.

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스 아이디 |

[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| path | String | O | `https://imagecdn.co.kr/sample_image.jpg` | URL Encoding된 이미지의 URL |

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | 총 검색 결과 개수 |
| data.query | String | O | `path=https://imagecdn.co.kr/sample_image.jpg` | 검색 질의 |
| data.items[].link | String | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | search by image에서 사용할 링크 |
| data.items[].center | float64 array | O | [0.825047801147227, 0.330948979591837] | 감지된 아이템의 중앙 x, y 좌표 % |
| data.items[].b0 | float64 array | O | [0.676864247418738, 0.219377551020408] | 감지된 아이템의 x0, y0 좌표 % |
| data.items[].b1 | float64 array | O | [0.973231355525813, 0.4426204081632654] | 감지된 아이템의 x1, y1 좌표 % |
| data.items[].score | float32 | O | 0.9732 | 감지된 아이템의 신뢰도 |

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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록되지 않은 서비스 아이디 |
| -45020 | ImageTooLargeException | 이미지 파일의 크기가 너무 큼<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45040 | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45050 | InvalidImageURLException | 접근할 수 없는 URL |
| -45060 | ImageTimeoutError | 이미지 다운로드 시간 초과 |
| -50000 | InternalServerError | 서버 오류 |

### Search By Image

* detect api에서 응답으로 받은 link를 기반으로 유사한 패션 아이템을 포함한 상품을 찾아주는 API

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스 아이디 |

[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | 최대 크기<br>1 이상 200 이하로 설정 가능 |
| link | string | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0%3D | detect API에서 전달받은 link (URL encoding 필요) |
| filter.category1_id | string | X | equal:3 | category1_id 값으로 필터링 |
| filter.category2_id | string | X | !equal:3 | category2_id 값으로 필터링 |
| filter.category3_id | string | X | !equal:3 | category3_id 값으로 필터링 |
| filter.s1 | string | X | equal:3 | s1 값으로 필터링 |
| filter.s2 | string | X | !equal:3 | s2 값으로 필터링 |
| threshold | float32 | X | 0.8 | 매칭 여부를 판단하는 유사도 기준값<br/> data.items[].similarity >= threshold인 항목만 매칭되는 것으로 판단합니다.<br/>0 초과 1.0 이하로 설정 가능 |

* filter.category1~3_id, filter.s1~2는 [필터링 가이드](./service-api-guide/#filtering-guide)에서 확인 가능

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image?limit=100&link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0%3D&filter.s1=equal:1"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | 총 검색 결과 개수 |
| data.query | String | O | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | 검색 질의 |
| data.items[].similarity | Number | O | 0.91234 | 검색 유사도 점수 |
| data.items[].productID | String | O | 8980335 | 상품 아이디 |

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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록되지 않은 서비스 아이디 |
| -45020 | ImageTooLargeException | 이미지 파일의 크기가 너무 큼<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45040 | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45050 | InvalidImageURLException | 접근할 수 없는 URL |
| -45060 | ImageTimeoutError | 이미지 다운로드 시간 초과 |
| -50000 | InternalServerError | 서버 오류 |

## 딥 태깅

<span id="tag-api"></span>
### Tag

* 입력 이미지에서 패션 아이템의 태그 정보를 감지하는 API입니다.

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |
| serviceID | 서비스 아이디 |

[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| path | String | O | `https://imagecdn.co.kr/sample_image.jpg` | URL Encode된 이미지 URL |
| lang | String | X | ko | label의 언어<br/>default: en<br/>en: English<br/>ko: Korean<br/>jp: Japanese |
| item_limit | int | X | 3 | 이미지에서 발견된 패션 아이템 중 태그 정보를 응답할 아이템 숫자<br/>아이템의 너비가 긴 순서로 정렬<br/>default: 1<br/>최대 크기<br>1 이상 4 이하로 설정 가능 |

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/tag?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg&lang=ko&item_limit=3"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 2 | 총 검색 결과 개수 |
| data.query | String | O | `path=https://imagecdn.co.kr/sample_image.jpg&lang=ko&item_limit=3` | 검색 질의 |
| data.items[].type | String | O | JACKET | 감지된 아이템의 type |
| data.items[].score | float32 | O | 0.9515 | 감지된 아이템의 신뢰도 |
| data.items[].tags | Array of json object | O |  | 감지된 아이템 태그 정보의 배열 |
| data.items[].tags[].attribute | String | O | category | 태그의 속성  |
| data.items[].tags[].labels | Array of json object | O |  | 태그 라벨의 배열 |
| data.items[].tags[].labels[].label | String | O | 블라우스 \| Blouse | 태그 라벨<br/>URL Parameter의 lang에 의해 응답 언어가 달라짐  |
| data.items[].tags[].labels[].score | float32 | O | 0.9545 | 태그 라벨의 신뢰도 |
| data.items[].center | float64 array | O | [0.825047801147227, 0.330948979591837] | 감지된 아이템의 중앙 x, y 좌표 % |
| data.items[].b0 | float64 array | O | [0.676864247418738, 0.219377551020408] | 감지된 아이템의 x0, y0 좌표 % |
| data.items[].b1 | float64 array | O | [0.973231355525813, 0.4426204081632654] | 감지된 아이템의 x1, y1 좌표 % |

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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록되지 않은 서비스 아이디 |
| -45020 | ImageTooLargeException | 이미지 파일의 크기가 너무 큼<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45040 | InvalidImageFormatException | 지원하지 않는 이미지 파일 형식<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45050 | InvalidImageURLException | 접근할 수 없는 URL |
| -45060 | ImageTimeoutError | 이미지 다운로드 시간 초과 |
| -50000 | InternalServerError | 서버 오류 |
