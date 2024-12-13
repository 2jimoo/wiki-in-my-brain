# Serializable
- 마커 인터페이스: Serializable은 메서드가 없는 마커 인터페이스입니다.
- 클래스가 이 인터페이스를 구현하면 JVM에 해당 객체가 직렬화 가능하다는 정보를 제공합니다.

# Object / entity.getClass()
Java에서 Object 타입의 매개변수를 전달하더라도, 해당 객체의 실제 런타임 클래스는 유지됩니다. 따라서 entity.getClass()를 호출하면 실제 객체의 클래스(예: Approval.class)를 반환합니다.
