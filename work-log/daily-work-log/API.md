# planned 기준 조회 필요하다고 하다가 이젠 closedAt 기준 필요하다는 경우 
- planned 기준 조회는 더 이상 사용하지 않으시는 건가요?
  - 네, 현재는 사용계획이 없습니다. 
- 사용하신다면 아래 방법 중 어떤 형식이 필요하실까요
  - 동일 API내 날짜 타입 파라미터 별로 planned/closedAt 조회 분기 제공
    - 문제1. 하위호환 문제로 기본 planned여야함
    - 문제2. 날짜검색 필드명에 plannedSearchStart/End로 이미 planned 정보가 들어가 있음 
  - closedAt 기준 조회하는 별도 API 제공
    - 문제. 날짜 타입별로 API가 늘어남  
  - ***타입 파라미터 별로 planned/closedAt 조회 분기 제공하는 별도 API 제공***
    - 문제. 비슷한 API가 하나 더 생김(그나마 사소)
    - SiController 이하 SA들이 모두 공유하는 건가?
      - ***원래는 SA별로 API 허용된 목록을 가져야하는데, 관리 시스템이 없으니 일단 허용 후 문서로 관리한다.***
- 페이지네이션 해도 되나여



# 목적 별 인터페이스 분리
- Detail 데이터를 가져오는게 목적이라면, ReminderTemplateDataDetailsResolver 같은 별도의 인터페이스로 분리하는게 좋을거 같아요.
