# CI/CD가 가져야할 특징과 기능?
- 비슷한 질문
  - 애플리케이션의 다운 타임을 최소화하기위한 배포방식은?
  - 서버 점검 페이지 없이 24/7 가용보장하기 위한 배포전략이나 시스템 구성은?
- 필수 조건
  - 무전지 서비스 및 빠른 롤백
    - 트래픽 원래 버전으로 라우팅
  - 장애 감지 및 관제
    - 배포 완료시 health check, server resource, metric 등 수집
  - 공통작업 자동화와 투명성

# 배포 방식
- Rolling Update
- Blue/Green
- Canary

# 배포 시스템 기능 정의
- 빌드버전 자동 생성
  - 업무티켓명-번호-모듈명-버전
- notification
- endpoint, resource 별  health check viewer
- 환경 별 progress viewer
- metric 수집과 모니터링
