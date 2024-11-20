# DDD
(Actor)Command, External System, Policy -> DomainEvent -> Policy -> EventHandler/Command..

# @TransactionalEventListener
- 트랜잭션에 포함된 이벤트 발행이 트랜잭션과 함께 롤백되길 바랄 때..
- @Transactional은 가장 밖 메소드에서만 유효(중첩 이득x)

# Predicate vs BiPredicate
- 인자 1/2개를 받아 bool 반환
- 상속이나 구현 관계 없음

# 인자 순서
- 필수 > 선택 > 부가정보 순

# URI vs URN
- URI(API, path)
  - 어디에 있는가
- URN(리소스 이름)
  - 무엇인가
  - 프로토콜:네임스페이스:식별자 


# LifeCycle, Status
- 위 조건 따진 후 action 수행

# CorelationId, ControlMode
- 작업 대상 조회시 위 조건으로 수행
- Domain은 자동생성/매뉴얼 생성 후 시스템과의 매핑이 변할 수 있음(위 조건으로 안 나올 수 있단 소리)


# Command/Query
- Query/Command에서는 Optional을 사용안하는게 좋을거 같습니다.
  - 왜냐하면 Validatior에서 처리를 못하고 때문에.정 필요시 @Nullable 어노테이션 정도
- 일반 사용자가 해당 파라미터를직접 입력하여 조회할 일이 있는가? 허용되는가?


# SystemCommand/Command/AdminCommand
- Name = Id
  - 자동 생성, 임의 생성
  - 엔티티 종류 등의 prefix
- 시스템은 Id기반 동작
- 사용자로부터 Id 노출하지 않기위해 사용자용 커맨드는 Name으로 받고, 내부는 Id기반 동작

# 패키지 구조
- port
  - in: Query, Command
  - out: repository
- entity.model
  - service, event...
- package-info.java
  - 프로젝트 관리와 문서화(주석으로 javadoc html 생성)
  - 패키지 수준의 애너테이션 사용
