# DDD
(Actor)Command, External System, Policy -> DomainEvent -> Policy -> EventHandler/Command..

# @TransactionalEventListener
- 트랜잭션에 포함된 이벤트 발행이 트랜잭션과 함께 롤백되길 바랄 때..

# Predicate vs BiPredicate
- 인자 1/2개를 받아 bool 반환
- 상속이나 구현 관계 없음
