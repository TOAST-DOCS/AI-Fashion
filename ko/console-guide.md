## AI Service > AI Fashion > 콘솔 사용 가이드

콘솔에서는 서비스를 생성하거나 삭제할 수 있으며,  색인 현황 및 검색 통계 모니터링을 위한 대시보드를 제공합니다.
AI Fashion의 카메라 검색, 유사 이미지 상품 추천 기능은 API로 제공되며, [서비스 API 가이드](./api-guide-v2.0)를 참고하여 구현할 수 있습니다.

콘솔의 이용 방법은 다음과 같습니다.

## 서비스 목록

### 서비스 생성
![create](http://static.toastoven.net/prod_ai_fashion/create_service_kr.png)
1. **서비스 생성**을 클릭합니다.
2. 서비스명을 입력합니다.
    - 서비스명에는 영어 소문자, 숫자, \_(밑줄), -(하이픈)만 사용할 수 있습니다.
    - 숫자, \_(밑줄), -(하이픈)으로 시작할 수 없습니다.
    - 2자 이상, 32자 이하로만 입력할 수 있습니다.
3. **저장**을 클릭합니다.

### 서비스 삭제
![delete](http://static.toastoven.net/prod_ai_fashion/delete_service_kr.png)
1. 서비스 목록에서 삭제할 서비스의 **삭제** 버튼을 클릭합니다.
    - 색인이 진행 중인 서비스는 삭제할 수 없습니다.
2. **삭제** 대화 상자가 나타나면 **확인** 버튼을 클릭합니다.

## 색인 현황 및 검색 통계 확인

### 색인 현황
서비스 목록에서 선택한 서비스의 색인 현황을 확인할 수 있습니다.
![indexStatus](http://static.toastoven.net/prod_ai_fashion/index_status_kr.png)
- 최근 로그순으로 30개까지 표시됩니다.

### 검색 통계
**검색 통계** 탭을 클릭하면 카메라 검색과 유사 이미지 상품 추천, 딥 태깅의 QC(Query Count, API를 호출한 횟수)를 차트로 확인할 수 있습니다.
![searchStats](http://static.toastoven.net/prod_ai_fashion/search_stats_kr.png)
- 전날부터 최근 3개월간의 검색 QC를 확인할 수 있습니다.
- .xls, .csv, .png, .jpeg 형식으로 파일을 다운로드할 수 있습니다.
