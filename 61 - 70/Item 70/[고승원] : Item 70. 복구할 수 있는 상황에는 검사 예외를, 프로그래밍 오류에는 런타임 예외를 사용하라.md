# Item 70. 복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라

자바에서 throwable 타입은 CheckedException, UnCheckedException, Error 이렇게 세가지가 있다.

### 어디서 어떻게 사용해야 적절한 사용일까?

- CheckedException : 호출하는 쪽에서 복구하리라 여겨지는 상황에 사용한다.
    - catch로 예외를 잡아 처리하는 경우
- UnCheckedException : 프로그래밍 오류를 나타낼 때 사용한다. (RuntimeException과 같다)
    - 전재 조건이 지켜지지 않은 경우 (ArrayIndexOutOfBound 등)
    - 순간적으로 트래픽이 몰려 생긴 에러는 잘 판단해야 한다.
- Error : JVM 자원 부족, 불변식 깨짐 등 더 이상 수행할 수 없는 상황에 사용한다.

CheckedException, UnCheckedException, Error를 상속하지 않은 Throwable는 만들지말자. 하지 말라면 그냥 하지말자. 가독성이 매우 떨어진다.

### 마무리

- RuntimeException = UnCheckedException
- Exception중 RuntimeException 제외하면 CheckedException
- Error는 다룰 일이 거의 없다.
