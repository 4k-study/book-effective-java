# Item 89. 인스턴스 수를 통제해야 한다면 readResolve 보다 enum을 사용하라.

싱글톤 패턴을 만들어도, Serializable을 구현하는 순간 싱글톤이 깨진다.

readObject 메서드를 제공하면 되지 않을까? 그렇지 않다. 전혀 다른 인스턴스를 반환하기 때문이다.

readResolve 기능을 이용하면 readObject가 만들어낸 인스턴스를 다른 것으로 대체할 수 있다.

readResolve 메서드를 적절히 정의해두면, 새로 생성된 인스턴스 대신 readResolve가 반환한 인스턴스를 반환한다.

### 주의점

- readResolve를 인스턴스 통제 목적으로 사용하려면 객체 참조 타입 인스턴스 필드는 모두 transient로 선언해야 한다.
    - 그렇지 않으면, readResolve 메서드가 실행되기 전에 역직렬화되어 공격당할 수 있다.
- readResolve 메서드의 접근성은 final 클래스인 경우 private으로 선언해야 한다.

enum 타입은 선언한 상수 외에 다른 객체가 없기 때문에 이런 문제가 생기지 않는다.
