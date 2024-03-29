# Item 73. 추상화 수준에 맞는 예외를 던져라

하위 레벨 내부에서 구현방식을 외부에 노출하면 상위 레벨 API를 오염시킬 수 있다.

**하위 레벨에서 구현 방식은 언제든 바뀔 수 있기** 때문이다. 이런 경우, 기존 클라이언트 **프로그램에 영향**을 끼치게 된다.

예외를 번역할 때(catch) **저수준 예외가 디버깅에 도움이 된다면 예외 연쇄를 사용해도 된다**. 그렇지 않다면, 추상화 수준에 맞는 예외를 사용하자.

* 예외 연쇄 : 문제의 cause를 고수준 예외에 실어 보내는 방식 (Throwable 생성자를 건네는 방식)

대부분의 표준 예외는 예외 연쇄용 생성자를 가지고 있지만, **예외 연쇄를 남발하는 방식은 좋지 않다**.

### 최고의 방법

- 저수준 메서드에서 예외가 발생하지 않도록 한다.
- 상위 계층 메서드의 매개변수를 미리 검사한다.
- 예외를 상위 계층으로 전파하기 전에 미리 처리한다. (로깅)
