
## AI Service > AI Fashion > 검색 API 가이드

* AI Fashion 검색 서비스를 사용하는 데 필요한 API를 설명합니다.

## API 공통 정보
### 사전 준비
- API 사용을 위해서는 프로젝트 통합 Appkey 또는 서비스 Appkey가 필요합니다. 
    - 프로젝트 통합 Appkey 사용을 권장합니다.
    - 프로젝트 통합 Appkey는 프로젝트 설정 페이지의 API 보안 설정에서 생성해 사용할 수 있습니다.
    - 서비스 Appkey는 콘솔 상단 **URL & Appkey** 메뉴에서 확인이 가능합니다.

### 요청 공통 정보
- API를 사용하기 위해서는 보안 키 인증 처리가 필요합니다.

[API 도메인]

| 환경 | 도메인 |
| --- | --- |
| Real | https://ai-fashion.cloud.toast.com |

<span id="input-image-guide"></span>
### 입력 이미지 가이드

* 입력 이미지를 다운 받는데, 3초 이상 걸리면 실패로 간주합니다.
* 입력 이미지의 패션 아이템의 너비와 높이가 둘 다 20px 이하인 경우는 인식하지 않습니다.
* 이미지 크기가 커질수록 패션 아이템의 크기도 커져야 더 정확하게 인식이 가능합니다.
* 이미지에서 패션 아이템이 차지하는 비중이 클수록 더 정확하게 인식이 가능합니다.
* 이미지 최대 크기: 최대 5,000,000 bytes
* 지원 이미지 포맷: PNG, JPEG, GIF

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

## API 목차

### 서비스 아이디 등록

* 서비스 아이디를 등록할 수 있는 API
* 최대 5개까지 등록이 가능.
* 허용 문자
    * 영어 소문자, 숫자, '-', '\_' 허용
    * 2\~32 글자 허용
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

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42010 | DuplicateServiceID | 중복된 서비스 아이디 |
| -42030 | ServiceIDQuotaExceededException | 허용된 서비스 아이디 개수 초과 |
| -50000 | InternalServerError | 서버 오류 |

### 서비스 아이디 삭제

* 등록된 서비스 아이디를 삭제할 수 있는 API
* 등록된 서비스 아이디에 색인 요청이 색인 진행 중에는 서비스 아이디를 삭제 할 수 없습니다.

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

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록 안 된 서비스 아이디 |
| -42020 | CannotDeletedOnIndexRunning | 색인 중에는 삭제할 수 없습니다 |
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
| data.totalCount | Number | O | 5 | 총 검색 결과 개수를 출력합니다. |
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

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -50000 | InternalServerError | 서버 오류 |

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
| limit | int | O | 100 | 최대 크기<br>0 < limit <= 200 |

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID}?limit=100"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | 총 검색 결과 개수를 출력합니다. |
| data.query | String | O | productID=10234455&limit=100 | 검색 질의를 출력합니다. |
| data.items[].similarity | Number | O | 0.91234 | 검색 적합도 점수를 출력합니다. |
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

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -40050 | NotFoundProductIDError | 상품 아이디가 없습니다 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록 안 된 서비스 아이디 |
| -50000 | InternalServerError | 서버 오류 |

### detect

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
| path | String | O | `https://imagecdn.co.kr/sample_image.jpg` | URL Encode된 image url |

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
| data.totalCount | Number | O | 100 | 총 검색 결과 개수를 출력합니다. |
| data.query | String | O | `path=https://imagecdn.co.kr/sample_image.jpg` | 검색 질의를 출력합니다. |
| data.items[].link | String | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | search by image 에서 사용할 링크 |
| data.items[].center | float64 array | O | [0.825047801147227, 0.330948979591837] | detect 된 아이템의 중앙 x, y 좌표 % |
| data.items[].b0 | float64 array | O | [0.676864247418738, 0.219377551020408] | detect 된 아이템의 x0, y0 좌표 % |
| data.items[].b1 | float64 array | O | [0.973231355525813, 0.4426204081632654] | detect 된 아이템의 x1, y1 좌표 % |
| data.items[].score | float32 | O | 0.9732 | detect 된 아이템의 신뢰도 |

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

                "link": "eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUiOiJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0==",
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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록 안 된 서비스 아이디 |
| -45020 | ImageTooLargeException | 이미지 파일의 크기가 너무 큼<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45040 | InvalidImageFormatException | 이미지 파일이 지원하지 않는 형식<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45050 | InvalidImageURLException | 접근할 수 없는 URL |
| -45060 | ImageTimeoutError | 이미지 다운로드 시간 초과 |
| -50000 | InternalServerError | 서버 오류 |

##### Response Code

* 항상 200

| Status | Description |
| --- | --- |
| 200 | OK |

### search by image

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
| limit | int | O | 100 | 최대 크기<br>0 < limit <= 200 |
| link | string | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | detect API 에서 전달받은 link |

<details><summary>요청 예</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image?limit=100&amp;link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0=="
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./service-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | 총 검색 결과 개수를 출력합니다. |
| data.query | String | O | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | 검색 질의를 출력합니다. |
| data.items[].similarity | Number | O | 0.91234 | 검색 적합도 점수를 출력합니다. |
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
        "query": "link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWfnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUiOiJBTEwiLpbnB1dHMi0lt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MyLCJ3aWa0aCI6MTU1LCJozWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0==&limit=100",
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

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 Appkey |
| -42000 | NotExistServiceID | 등록 안 된 서비스 아이디 |
| -45020 | ImageTooLargeException | 이미지 파일의 크기가 너무 큼<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45040 | InvalidImageFormatException | 이미지 파일이 지원하지 않는 형식<br>[입력 이미지 가이드](./service-api-guide/#input-image-guide) 참고 |
| -45050 | InvalidImageURLException | 접근할 수 없는 URL |
| -45060 | ImageTimeoutError | 이미지 다운로드 시간 초과 |
| -50000 | InternalServerError | 서버 오류 |

