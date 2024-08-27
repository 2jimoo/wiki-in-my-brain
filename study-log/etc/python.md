[@dataclass](https://github.com/2jimoo/wiki-in-my-brain/blob/main/study-log/etc/python.md#dataclass-1)  
[object](https://github.com/2jimoo/wiki-in-my-brain/blob/main/study-log/etc/python.md#object)

--- 


# @dataclass
- from dataclasses import dataclass
-  __init__(), __repr__(), __eq__()와 같은 메서드 자동 생성

# object
- 파이썬 3에서는 모든 클래스가 자동으로 object를 상속받음(이전 버전에서는 명시적 상속 필요)
- 파이썬의 모든 클래스가 공통적으로 가지는 기본적인 기능을 제공
  - __new__
    - 객체를 생성하고 반환
  - __init__(self, ...)
    - 객체가 생성될 때 호출되는 초기화 메서드.
  - __repr__(self)
    - 객체의 문자열 표현을 반환. 주로 개발, 디버깅용.
  - __str__(self)
    - 사용자에게 객체를 보여주기 위한 문자열 표현을 반환. 주로 print() 함수에서 사용.
  - __eq__(self, other)
    - 객체 간의 동등성을 비교. == 연산자와 관련.
  - __ne__(self, other)
    - 객체 간의 불동등성을 비교합니다. != 연산자와 관련.
  - __hash__(self)
    - 객체의 해시 값을 반환. 해시 가능한 객체에서 사용.
  - __dict__
    - __dict__는 객체의 속성과 그 값을 저장하는 딕셔너리. 이를 통해 클래스 속성을 동적으로 관리
