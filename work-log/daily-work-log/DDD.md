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
